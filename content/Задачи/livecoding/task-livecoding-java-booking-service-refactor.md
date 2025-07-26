#### 64. Рефакторинг BookingService для корректного бронирования

Дан код с ошибками. Сервис бронирования номеров. У Room есть статус: забронирован или свободен. Метод bookRoom() семантически написан правильно. Но надо подумать, почему этот код пройдет все тестовые стенды, но будет косячить на проде? Можно порефакторить

```java
@Service
class BookingService {
    @Autowired
    private RoomRepository roomRepository;

    public boolean bookRoom(Integer roomId) {
        boolean roomBooked = false;

        Room room = roomRepository.findById(roomId);
        if(room.getStatus() == ""VACANT"") {
            room.setStatus(""BOOKED"");
            room.setClientId(SecurityContext.getClientId());
            roomRepository.save(room);
            roomBooked = true;
        }
        return roomBooked;
    }
}


@Entity
class Room {
    @Id
    Integer id;
    String status; // VACANT, BOOKED
    String clientId;
    String roomNumber;
}

public interface RoomRepository extends JpaRepository<Room, Integer> {

}

```

{{< hint warning >}}  
**Спойлеры к решению**  
{{< /hint >}}

{{< details "Подсказки" close >}}  
💡 Состояние гонки возникает из‑за отсутствия **транзакций** и **блокировок** при чтении/записи.  
💡 Нужно обернуть логику в `@Transactional`, чтобы всё происходило в одной транзакции.  
💡 Для гарантии атомарного обновления использовать **пессимистическую** или **оптимистическую блокировку** (версия в сущности).  
💡 Альтернатива: добавить в `RoomRepository` метод `@Modifying @Query` с условием `WHERE status = 'VACANT'` и проверять количество обновлённых строк.  
{{< /details >}}

{{< details "Решение" close >}}

```java
import javax.persistence.*;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Entity
class Room {
    @Id
    private Integer id;

    @Version
    private Long version;           // для оптимистической блокировки

    private String status;          // VACANT, BOOKED
    private String clientId;
    private String roomNumber;
    // геттеры/сеттеры omitted
}

public interface RoomRepository extends JpaRepository<Room, Integer> {
    // Вариант с модифицирующим запросом:
    @Modifying
    @Query("UPDATE Room r SET r.status = 'BOOKED', r.clientId = :clientId WHERE r.id = :roomId AND r.status = 'VACANT'")
    int bookIfVacant(@Param("roomId") Integer roomId, @Param("clientId") String clientId);
}

@Service
public class BookingService {
    private final RoomRepository roomRepository;

    public BookingService(RoomRepository roomRepository) {
        this.roomRepository = roomRepository;
    }

    // Вариант 1: Используем модифицирующий запрос — атомарность на уровне БД
    @Transactional
    public boolean bookRoom(Integer roomId) {
        String clientId = SecurityContext.getClientId();
        int updated = roomRepository.bookIfVacant(roomId, clientId);
        return updated == 1;
    }

    // Вариант 2: Оптимистическая блокировка через @Version
    @Transactional
    public boolean bookRoomOptimistic(Integer roomId) {
        Room room = roomRepository.findById(roomId)
            .orElseThrow(() -> new EntityNotFoundException("Room not found: " + roomId));
        if (!"VACANT".equals(room.getStatus())) {
            return false;
        }
        room.setStatus("BOOKED");
        room.setClientId(SecurityContext.getClientId());
        roomRepository.save(room); // при конфликте выбросит OptimisticLockException
        return true;
    }
}
```

{{< /details >}}
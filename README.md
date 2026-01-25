git config -f .gitmodules --remove-section submodule.themes/hugo-book   # java-backend-interview-prep

## Локальный запуск

Установить Hugo 

https://gohugo.io/installation/

Склонировать проект

```bash
git clone --recurse-submodules git@github.com:zhukovsd/java-backend-interview-prep.git
```

Зайти в директорию проекта

```bash
cd java-backend-interview-prep
```
Запустить локальный сервер с проектом

```bash
hugo serve
```

Проект будет доступен на localhost:1313. Любое изменение в файлах проекта будет триггерить пересборку, т.е. все изменения будут отображаться на localhost

---

Новый контент можно добавлять с помощью Hugo для правильной работы с метадатой в .md файлах

```bash
hugo new content content/{file_name.md}
```

Сайт:
> https://zhukovsd.github.io/java-backend-interview-prep/

## Добавление новой категории вопросов

1. Добавить `.md` файл с контентом аналогичным другим файлам с категориями
2. Добавить пустой `.toml` файл который находится по тому же пути и с таким же названием как ранее созданный `.md` файл  
   Если файл категории создан по пути `/content/Прочее/FP.md`  
   То `.toml` файл должен быть создан по пути `/data/Прочее/FP.toml`  
3. Добавить новую категрию в `data-generator-config.toml`
   ```toml
   [[categories]]
   name = "Функциональное программирование"
   answers_filepath = "Прочее/FP.md"
   ```
4. Добавить новый лист в Google таблицу с ответам. Название листа соответствует паттерну в `data-generator-config.toml`. Текущий паттерн `Ответы - {}`, значит название листа должно быть `Ответы - Функциональное программирование`
5. Прогнать пайплайн для заполнения нового `.toml` файла данными из таблицы, смерджить PR

# Лабораторная работа №31-32. SQLite и Entity Framework Core

## Студент
- **ФИО:** Уютов Павел Александрович
- **Группа:** ИСП-231
- **Дата:** 31.05.2026

## Описание
Веб-API на ASP.NET Core с подключением SQLite через Entity Framework Core.
Реализован CRUD, LINQ-запросы, миграции, пагинация, поиск и статистика.

## Команды dotnet ef
```bash
dotnet ef migrations add НазваниеМиграции
dotnet ef database update
dotnet ef migrations list
dotnet ef migrations remove
dotnet ef database update --verbose
```

## Структура проекта
- `Program.cs` - настройка сервисов, DbContext, Swagger
- `TaskDb.csproj` - файл проекта
- `Models/TaskItem.cs` - модель задачи
- `Models/TaskDtos.cs` - DTO для создания и обновления
- `Data/AppDbContext.cs` - контекст базы данных
- `Controllers/TasksController.cs` - контроллер с маршрутами
- `Migrations/` - миграции EF Core
- `taskdb.db` - бд
- `img/` - скрины
- `README.md` - описание

## Маршруты API
| Метод | Маршрут | Описание |
|-------|---------|----------|
| GET | /api/tasks | Все задачи |
| GET | /api/tasks/{id} | Задача по id |
| POST | /api/tasks | Создать задачу |
| PUT | /api/tasks/{id} | Обновить задачу |
| PATCH | /api/tasks/{id}/complete | Переключить статус |
| DELETE | /api/tasks/{id} | Удалить задачу |
| GET | /api/tasks/search | Поиск по параметрам |
| GET | /api/tasks/stats | Статистика |
| GET | /api/tasks/paged | Пагинация |
| GET | /api/tasks/overdue | Просроченные задачи |
| PATCH | /api/tasks/complete-all | Выполнить все |
| DELETE | /api/tasks/completed | Удалить выполненные |

## Миграции
| Миграция | Описание |
|----------|----------|
| InitialCreate | Создание таблицы Tasks |
| AddDueDateToTask | Добавление поля DueDate |

## LINQ vs SQL
| LINQ | SQL |
|------|-----|
| .Where(t => t.IsCompleted) | WHERE is_completed = 1 |
| .OrderByDescending(t => t.CreatedAt) | ORDER BY created_at DESC |
| .Take(10) | LIMIT 10 |
| .Skip(5).Take(10) | OFFSET 5 LIMIT 10 |
| .CountAsync() | SELECT COUNT(*) |
| .GroupBy(t => t.Priority) | GROUP BY priority |

## Сравнение: хранение в памяти vs EF Core + SQLite

| Концепция | Хранение в памяти | EF Core + SQLite |
|-----------|-------------------|------------------|
| Хранение данных | static List<T> в RAM | Файл .db на диске |
| После перезапуска | Данные пропадают | Данные сохраняются |
| Поиск по условию | LINQ to Objects | LINQ to Entities → SQL |
| Создание структуры | Не нужно | Миграции (dotnet ef) |
| Начальные данные | Хардкод в коде | HasData() в миграции |
| Получение данных | list.FirstOrDefault(...) | await db.Table.FindAsync(id) |
| Добавление | list.Add(item) | db.Table.Add(item) + SaveChangesAsync() |
| Удаление | list.Remove(item) | db.Table.Remove(item) + SaveChangesAsync() |
| Масштабируемость | Ограничена RAM | Гигабайты данных |
| Транзакции | Нет | Встроены в EF Core |

## Выводы
1. EF Core переводит LINQ в SQL автоматически — не нужно писать SQL вручную.
2. Миграции — это система контроля версий для структуры БД.
3. Code First удобнее: изменил класс → создал миграцию → база обновлена.
4. SaveChangesAsync() — ключевой момент, до него изменения только в памяти.
5. async/await при работе с БД — стандарт, блокировать поток плохая практика.
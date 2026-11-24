# Диаграммы

[К кейсу](../README.md)

## Sequence: запрос рецепта (AS-IS)

```mermaid
sequenceDiagram
 actor G as Гость
 participant API as Next.js
 participant DB as PostgreSQL
 actor M as Мастер
 G->>API: GET активная сессия
 API->>DB: Найти последнюю активную
 API-->>G: Состав и крепость
 G->>API: Запрос рецепта
 API->>DB: mixRequested=true
 API-->>M: SSE mix_requested
 M->>API: Сохранить рецепт
 API->>DB: Записать рецепт и снять флаг
 API-->>G: SSE recipe_ready
 G->>API: Перечитать сессию
 API-->>G: Рецепт
```

## Жизненный цикл сессии (концептуальное отображение флагов)

```mermaid
stateDiagram-v2
 [*] --> ActivePending
 ActivePending --> Active: Открытие панели
 ActivePending --> Inactive: Новая сессия или освобождение
 Active --> Inactive: Новая сессия или освобождение
 Inactive --> [*]
```

masterCalled и mixRequested — независимые флаги, не состояния взаимоисключающей машины. Диаграмма отображает нормальный сценарий интерфейса, не все разрешённые переходы API.

## AI-assisted ввод

```mermaid
flowchart TD
 A[Запись команды] --> B[Транскрибация]
 B --> C[LLM структурирует текст]
 C --> D[Черновик формы]
 D --> E{Мастер подтверждает?}
 E -->|Нужны правки| F[Ручная коррекция]
 F --> D
 E -->|Да| G[Создание сессии]
 B -. Ошибка .-> F
 C -. Ошибка .-> F
```

## BPMN 2.0

[Открыть исходник BPMN](recipe-request.bpmn) в bpmn.io / Camunda Modeler. Упрощённый процесс запроса рецепта с дорожками гостя, системы и мастера. Графические схемы Mermaid выше — не BPMN.

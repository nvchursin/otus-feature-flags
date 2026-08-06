# Frontend Project Structure

## Overview

Поскольку в админке для управления флагами всего две страницы, здесь будет достаточно упрощённой fsd-структуры.

## Structure

```text
📂 src
├── 📂 app
│   ├── 📂 router
│   ├── 📂 styles
│   └── 📄 app.tsx
│   └── 📄 index.ts
│
├── 📂 pages
│   ├── 📂 flags
│   │   ├── 📂 ui
│   │   ├── 📂 model
│   │   └── 📄 index.ts
│   └── 📂 flag
│       ├── 📂 ui
│       ├── 📂 model
│       └── 📄 index.ts
│
├── 📂 entities
│   └── 📂 flag
│       ├── 📂 api
│       ├── 📂 model
│       ├── 📂 ui
│       └── 📄 index.ts
│
├── 📂 shared
│   ├── 📂 lib
│   └── 📂 ui
│
└── 📄 main.tsx
```

## Description

- **app** — корневой компонент приложения и конфигурация `react-router`;
- **pages** — две страницы: таблица всех фича-флагов и страница
  конкретного флага;
- **entities** — доменная сущность фича-флага, типы, api, effector-модель и
  базовые компоненты, контейнеры;
- **shared** — переиспользуемые сущности: ui-компоненты, библиотечный код и разнообразные утилиты и хелперы.

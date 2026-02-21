---
name: project-plan
description: >
  Нарезка user stories из PRD и настройка структуры проекта.
  Работает и для старта нового проекта, и для нового этапа существующего.
  Триггеры: "/project-plan", "нарезай стори", "новый этап проекта", "следующий этап проекта".
---

# Project Plan

Извлекает user stories из PRD, создаёт (или дополняет) структуру управления проектом.

## Два сценария

### A. Новый проект (файлов нет)

1. Проверить `docs/prd.md` — если нет, вызвать `/prd`
2. Извлечь user stories из PRD → создать md-файлы в `docs/stories/`
3. Создать `project_state.md` (формат из CLAUDE.md, раздел «project_state.md»)
4. Создать `docs/plans/`, `docs/reviews/`, `docs/stories/done/`

### B. Новый этап (файлы существуют)

1. Прочитать текущие `docs/prd.md`, `docs/stories/`, `docs/issues/`, `project_state.md`
2. Спросить пользователя: что нового? (новые требования, изменения скоупа)
3. Обновить PRD если нужно
4. Извлечь **новые** stories → создать новые md-файлы в `docs/stories/`
5. Нумерация продолжает существующую (если US-009 последняя → новые с US-010)
6. Обновить `project_state.md`: Backlog, Architecture (если изменилась)

## Структура файлов

```
docs/
├── prd.md                 # Product Requirements Document
├── stories/               # User Stories — функциональность (source of truth)
│   ├── US-010.md          # Активные стори
│   └── done/              # Завершённые стори
│       └── US-001.md
├── issues/                # Issues — баги, доработки, tech debt
│   ├── ISS-001.md         # Активные issues
│   └── done/              # Закрытые issues
│       └── ISS-000.md
├── plans/                 # Планы для каждой story (US-XXX-название.md)
└── reviews/               # Ревью планов и кода (US-XXX-plan-review.md, US-XXX-code-review.md)
project_state.md           # Слепок состояния (формат из CLAUDE.md)
```

## Разделение stories и issues

- **docs/stories/** — КТО и ЧТО хочет получить (бизнес-ценность). Формат: "Как <роль> я хочу <действие>, чтобы <цель>".
- **docs/issues/** — ТЕХНИЧЕСКИЕ задачи: баги, доработки, tech debt. Не имеют бизнес-формулировки.

При создании новой задачи определи тип:
- Новая функциональность для пользователя → md-файл в `docs/stories/`
- Баг, исправление, улучшение, рефакторинг → md-файл в `docs/issues/`

## Формат user story (md-файл)

Каждая стори — отдельный файл `docs/stories/US-XXX.md`. Завершённые — в `docs/stories/done/`.

```markdown
# US-XXX: Название

**Phase:** mvp | v2
**Priority:** N

## Description

Как <роль> я хочу <действие>, чтобы <цель>

## Acceptance Criteria

- [ ] Критерий 1
- [ ] Критерий 2

## Technical Notes

Технические детали реализации.

## Related Files

- `path/to/file.py`
```

Для завершённых стори чекбоксы заполнены: `- [x]`.

## Формат issue (md-файл)

Каждый issue — отдельный файл `docs/issues/ISS-XXX.md`. Закрытые — в `docs/issues/done/`.

```markdown
# ISS-XXX: Название

**Type:** bug | enhancement | tech_debt
**Priority:** high | medium | low
**Related US:** US-XXX или —

## Description

Описание проблемы.

## Technical Notes

Технические детали.
```

## Правила извлечения stories

- Каждая story = один вертикальный слайс пользовательской ценности
- Формат описания: "Как <роль> я хочу <действие>, чтобы <цель>"
- Acceptance criteria включают Definition of Done
- Новые стори создаются в `docs/stories/`, завершённые переносятся в `docs/stories/done/`

## Финализация

- Показать что создано/обновлено
- Предложить начать с первой story из бэклога

## Зависимости

- Skill `/prd` — для создания PRD, если его нет

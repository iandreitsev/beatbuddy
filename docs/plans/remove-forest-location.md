# План: Удалить локацию «Лес»

## Цель
Убрать ForestLocation из приложения. Остаются две локации: Default и Runner.

## Что удаляем

### 1. Файл локации
- `src/web/js/locations/ForestLocation.js` — удалить целиком

### 2. Импорт в Animator
- `src/web/js/Animator.js:10` — убрать строку `import './locations/ForestLocation.js'`

### 3. project_state.md
- Убрать строку `ForestLocation — лесная сцена (деревья, цветы, бабочки, эффекты ударов)`

## Что НЕ трогаем
- `LocationRegistry.js` — не нужно менять; ForestLocation регистрируется через side-effect import, без импорта `register('forest', ...)` просто не вызовется
- `app.js` — нет прямых ссылок на forest
- Ассеты — ForestLocation рисует процедурно (PixiJS Graphics), файлов ассетов нет
- Документация (`docs/plans/US-013-лес.md`, ревью) — исторические артефакты, не трогаем
- `docs/prd.md` — оставляем как есть; PRD описывает продуктовое видение и историю решений, а не текущее состояние кода (для этого есть `project_state.md`)

## Побочные эффекты (legacy localStorage)

Если у пользователя в `localStorage` сохранён `locationId: "forest"`:

1. `loadSettings()` читает `locationId: "forest"` из localStorage
2. `locationSelect.value = "forest"` — но `<option value="forest">` больше нет в select
3. Браузер сбрасывает `.value` в `""` (пустая строка), визуально select показывает первый пункт
4. При старте: `onStartClick()` берёт `locationSelect.value` → `""` → `LocationRegistry.create("")` → fallback на `"default"` (строка 41 LocationRegistry.js)
5. После первого `saveSettings()` значение в localStorage перезапишется на актуальное

**Итог:** пользователь увидит Default-локацию, ошибок не будет. При следующем сохранении настроек legacy-значение исчезнет.

## Порядок действий
1. Удалить `src/web/js/locations/ForestLocation.js`
2. Убрать импорт в `Animator.js`
3. Обновить `project_state.md`
4. Smoke-тест в браузере (см. Acceptance Criteria)

## Acceptance Criteria
- [ ] Select на Setup Screen содержит ровно 2 локации (Default, Runner) — «Лес» отсутствует
- [ ] Сессия с локацией Default: запуск → метроном играет → удары регистрируются → стоп → статистика показана. Без ошибок в консоли
- [ ] Сессия с локацией Runner: запуск → анимация работает → удары регистрируются → стоп → статистика показана. Без ошибок в консоли
- [ ] Legacy fallback: вручную задать `localStorage.setItem('beatBuddySettings', JSON.stringify({locationId:"forest"}))`, перезагрузить — select показывает первый пункт, сессия стартует на Default без ошибок

# American Express — Insurance Page (Контекст проєкту)

## Що це таке
Одна HTML-сторінка (`index.html`) — страховий документ для клієнтів при морському перевезенні автомобілів. Клієнт читає умови, обирає рівень страховки, ставить підпис онлайн. Дані автоматично зберігаються у Google Sheets.

## Розташування
- **Локальний файл:** `C:\Users\User\Desktop\Страхування код\index.html`
- **GitHub репо:** https://github.com/Daniil4449/American-express-insurance
- **Сайт (GitHub Pages):** https://daniil4449.github.io/American-express-insurance/

## Структура сторінки
Лівий сайдбар — ЗМІСТ з посиланнями на секції 1–9 + форма.

| Секція | Зміст |
|--------|-------|
| 1 | Загальні положення та сфера дії |
| 2 | Географія покриття |
| 3 | Рівні страхування (Мінімум/Стандарт/Преміум) |
| 4 | RUN AND DRIVE |
| 5 | Що не покривається |
| 6 | Порядок подачі заяви |
| 7 | Додаткові умови |
| 8 | Підписання та набуття чинності |
| 9 | Вологопоглиначі ($40) |
| Форма | Підтвердження та підпис |

## Форма підписання
- Вибір рівня страховки (5 варіантів)
- Картка **Вологопоглиначі** ($40) — увімкнена за замовчуванням, можна зняти
- Поля: ПІБ, телефон, марка авто, модель, VIN, дата прибуття
- Підпис на canvas
- Чекбокс "Ознайомився"
- Кнопка "Підтвердити та зберегти"
- Після відправки: картка підтвердження + кнопки PDF / Друк

## Google Sheets інтеграція

**Таблиця:** https://docs.google.com/spreadsheets/d/1lioXtCy4sY8q-L2103ZfuCqKaM6-_633ijLob7gzbnI/edit

**Колонки:**
| A | B | C | D | E | F | G | H |
|---|---|---|---|---|---|---|---|
| Дата | VIN / №замовлення | Ім'я | Телефон | Марка авто | Рівень страховки | Підпис (фото) | Вологопоглиначі |

**Apps Script (активний deploy URL):**
```
https://script.google.com/macros/s/AKfycbxHtt32KcuN3erB2POxS8FHWaPb1MztoijZSx5T6Yaqe0OSRTqsI0z_KxvD8wsWOdiC/exec
```

**Apps Script editor:**
https://script.google.com/u/0/home/projects/1fnBo1yQkXDV7HQTRxCjJQ6wMlrh2tCpueTke1b3JNmfT2W7glQNb3qc0/edit

**Поточний код скрипту:**
```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  if (sheet.getLastRow() === 0) {
    sheet.appendRow(['Дата', 'VIN / №замовлення', "Ім'я", 'Телефон', 'Марка авто', 'Рівень страховки', 'Підпис (фото)', 'Вологопоглиначі']);
  }

  sheet.appendRow([
    data.date,
    data.vin,
    data.name,
    data.phone,
    data.car,
    data.level,
    data.sigImg ? '✓ є' : '-',
    data.moisture,
  ]);

  return ContentService.createTextOutput('OK');
}
```

## Важливо при змінах в Apps Script
Кожного разу коли змінюється скрипт і розгортається нова версія — **змінюється URL деплою**.
Після отримання нового URL потрібно оновити рядок `SHEET_URL` в `index.html` (~рядок 834) і запушити на GitHub.

## GitHub push
Акаунт: `Daniil4449`  
Якщо push падає через прострочений токен — витягти OAuth токен з Windows Credential Manager:
```powershell
# (PowerShell) — витягує токен зі сховища
```
Детальніше: файл `memory/feedback_github_push.md`

## Технологічний стек
- Чистий HTML/CSS/JS (один файл, без фреймворків)
- jsPDF + html2canvas — генерація PDF
- Canvas API — підпис клієнта
- Fetch API — відправка даних у Google Sheets
- Google Apps Script — прийом POST і запис у таблицю

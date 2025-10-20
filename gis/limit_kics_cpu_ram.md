📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **июнь 2024**

# ⚙️ Ограничение потребления ресурсов CPU и RAM в KICS

> 💡 Эта настройка особенно полезна на **АРМах АСУТП**, где работает **SCADA** — антивирус не должен мешать основной работе!

---

## 🧠 Ограничение загрузки CPU

Для задач проверки по требованию (сканирование файлов, контейнеров и т.д.) можно задать приоритет выполнения через параметр **`ScanPriority`**:

| Значение | Эффект |
|----------|--------|
| `Idle`   | ≤10% одного ядра CPU |
| `Normal` | 50% всех ядер |
| `High`   | Без ограничений |

> 🔽 **Рекомендуется**: `Idle` — чтобы не мешать работе промышленных систем.

### 📌 Как применить

1. Определите ID задач, которые нагружают CPU:
   - `2` — Scan_My_Computer  
   - `3` — Scan_File  
   - `4` — Critical_Areas_Scan  
   - `16` — Removable_Drives_Scan  
   - `18`, `19` — Container_Scan  
   - `22` — Inventory_Scan  

   *(Полный список: [официальная документация KICS](https://support.kaspersky.com/KICS4Linux/1.3/ru-RU/8540.htm))*

2. Установите приоритет `Idle` для каждой задачи:
   ```bash
   kics-control --set-settings 2 ScanPriority=Idle
   kics-control --set-settings 3 ScanPriority=Idle
   kics-control --set-settings 4 ScanPriority=Idle
   kics-control --set-settings 16 ScanPriority=Idle
   kics-control --set-settings 18 ScanPriority=Idle
   kics-control --set-settings 19 ScanPriority=Idle
   kics-control --set-settings 22 ScanPriority=Idle
   ```

> ⏱️ После запуска сканирования загрузка CPU на 1–2 секунды может быть высокой, но затем снизится до 10% (см. скриншот ниже).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/wtpk78atr5002.jpg)

---

## 💾 Ограничение использования оперативной памяти

По умолчанию KICS использует до **40% RAM** (но не менее 2 ГБ) при распаковке архивов во время сканирования.  
На серверах с сотнями ГБ ОЗУ это может быть избыточно.

### 🛠️ Как ограничить RAM

1. Остановите службу:
   ```bash
   sudo systemctl stop kics
   ```

2. Откройте конфигурационный файл:
   ```bash
   sudo nano /var/opt/kaspersky/kics/common/kics.ini
   ```

3. В секции `[General]` добавьте или измените параметр (значение в **мегабайтах**):
   ```ini
   ScanMemoryLimit=8192
   ```
   → Пример: `8192` = 8 ГБ

4. Запустите службу:
   ```bash
   sudo systemctl start kics
   ```

> ⚠️ **Важно!**  
> Параметр `ScanMemoryLimit` ограничивает **только память, используемую при сканировании**, а **не общий объём памяти**, потребляемый KICS.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nbeajngei0002.jpg)

---

## 🎉 Завершение

> **На этом всё!**  
> KICS теперь работает в фоновом режиме, не мешая SCADA и другим критичным процессам.
> 🧟 **Совет**: На АСУТП всегда тестируйте настройки в макетной среде перед применением на промышленных объектах!

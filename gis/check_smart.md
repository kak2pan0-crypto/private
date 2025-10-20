# 💾 Проверка диска S.M.A.R.T.

📝 **Автор**: Грубич В.Е.  <br>
📅 **Дата создания**: октябрь 2024 <br>

---

## 🛠️ Установка утилиты

По умолчанию пакет мониторинга не установлен. Установите `smartmontools`:

```bash
# Для Debian/Ubuntu/Astra
sudo apt install smartmontools

# Для RedOS/RHEL
sudo yum install smartmontools
```

---

## 🔍 Определение дисков

Узнайте имена ваших дисков:

```bash
fdisk -l
```

Пример вывода:
- `/dev/sdb` — HDD
- `/dev/sdc` — SSD

---

## 🖥️ Проверка HDD через SMART

Получите полную информацию о диске:

```bash
sudo smartctl -a /dev/sdb
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/npk5w4p7xj.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qs95hjjcxm.jpg)

### ⚠️ Критические параметры для HDD:

| ID  | Параметр                     | Описание |
|-----|------------------------------|----------|
| **5**   | `Reallocated_Sector_Ct`      | Высокое значение = много повреждённых секторов |
| **197** | `Current_Pending_Sector`     | Проблемы с чтением данных |
| **187** | `Reported_Uncorrect`         | Неисправимые ошибки |
| **198** | `Offline_Uncorrectable`      | Неисправимые ошибки при офлайн-тесте |
| **199** | `UDMA_CRC_Error_Count`       | Проблемы с кабелем или интерфейсом |

> 💡 Также могут вызывать подозрения:  
> **01, 07, 10, 184, 188, 195, 200**  
> *(у некоторых моделей эти параметры накопительные и не критичны)*

---

## 💿 Проверка SSD через SMART

Для SSD используйте ту же команду:

```bash
sudo smartctl -a /dev/sdc
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4es05ncfxp.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/lf33wb7kf7.jpg)

### ⚠️ Критические параметры для SSD:

| Параметр | Описание |
|----------|----------|
| **Media_Wearout_Indicator** | Износ NAND-ячеек (ниже 10% — критично) |
| **Available_Spare**         | Запасные ячейки (ниже 10% — замена диска) |
| **Percentage_Lifetime_Used**| Процент износа (100% = диск исчерпан) |
| **Uncorrectable_Error_Count**| Неисправимые ошибки чтения/записи |

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fdaajix7t3.jpg)

---

## 🎉 Завершение

> **На этом всё!**  
> Регулярная проверка SMART поможет вовремя выявить проблемы с дисками.
> 💾 **Совет**: Настройте автоматическую проверку SMART через `cron` или мониторинговую систему (например, Zabbix).

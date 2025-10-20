# 💽 Разметка диска вручную в Astra Linux

📝 **Автор**: Грубич В.Е.  
📅 **Дата создания**: декабрь 2024

> ⚠️ **Предполагается**, что RAID уже собран и вы находитесь на этапе установки ОС — **разметка дисков**.

---

## 🧭 Общая схема

После запуска установки Astra Linux:
- Выберите **«Вручную»** при разметке дисков.
- В списке появятся все доступные диски, например:
  - `sda` — RAID1 (для системы)
  - `sdb` — RAID6 (для хранилища /backup или `/var/lib/jatoba`)

> 🔍 Обратите внимание: в списке могут отображаться **флешки, загрузочные носители и другие устройства**.  
> ❌ **Не удаляйте их случайно!**  
> Ищите диски с пометкой **AVAGO** (или другого вашего RAID-контроллера).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/53ysjue4pw.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g9cau6joff.jpg)

---

## 🛠️ Пошаговая разметка

### 1. Подготовка диска `sda` (RAID1 — система)

1. Выберите `sda` → **«Создать пустую таблицу разделов»** → `Enter`  
   > 💡 Даже если разделов нет — всё равно создайте таблицу!

2. Выберите **«Свободное место»** → `Enter`

3. Создайте разделы **в строгом порядке**:

| Точка монтирования | Назначение | Размер |
|--------------------|-----------|--------|
| — | **EFI System Partition** | `1024 МБ` |
| `/boot` | Загрузочный раздел | `10 ГБ` |
| `/` | Корневая файловая система | **остаток** (рассчитайте заранее!) |
| `/var/log/audit` | Аудит-логи | `70–100 ГБ` (на ЗПКТ — 15 ГБ) |
| — | **Раздел подкачки (swap)** | `2 × RAM` (обычно до 16 ГБ) |

> 📌 **Важно**:  
> - Для `/var/log/audit` укажите точку монтирования **вручную**.  
> - Для swap выберите тип: **«раздел подкачки»**.  
> - Давайте **имена разделам** для удобства.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hf96c5361b.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fr7lf4ode7.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bdwjmlxv2t.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/lcnto2myjj.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/guawfy9k6g.jpg)

---

### 2. Разметка диска `sdb` (RAID6 — данные)

- Выберите `sdb` → создайте таблицу разделов.
- Создайте **один раздел** на всё пространство:
  - Для **Cyber Backup**: точка монтирования — `/backup`
  - Для **ITM-K / Jatoba**: точка монтирования — `/var/lib/jatoba`

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/famcobk2dc.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/x74fho1kq4.jpg)

---

## ✅ Завершение

1. Выберите **«Закончить разметку и записать изменения»**.
2. Подтвердите запись — начнётся установка ОС.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/6th0yg7ryp.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/62hugigxv6.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/9mgsw8hq7p.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hqlmivt4w1.jpg)

---

## 📋 Пример разметки

```text
sda — RAID1 (система):
  sda1 — efi            → 1024 MB
  sda2 — /boot          → 10 GB
  sda3 — /              → остаток (~80–200+ GB)
  sda4 — /var/log/audit → 70–100 GB (15 GB на ЗПКТ)
  sda5 — swap           → 16 GB (или 2×RAM)

sdb — RAID6 (данные):
  sdb1 — /backup        → всё пространство   ← для Cyber Backup
  или
  sdb1 — /var/lib/jatoba → всё пространство ← для ITM-K
```

> ⚠️ **Размер корневого раздела `/` нужно рассчитать заранее**, чтобы хватило места под ОС и приложения!

---

## 🎥 Дополнительно

📹 **[Видео: Разметка дисков в Астре вручную](Разметка%20дисков%20в%20Астре%20вручную.mkv)**  
*(файл доступен локально в папке проекта)*

---

## 🎉 Завершение

> **На этом всё!**  <br>
> Разметка выполнена, установка продолжится автоматически. <br>
> ✈️ **Совет**: На промышленных объектах всегда сверяйтесь с **Рабочей Документацией (РД)** — требования к разметке могут отличаться.

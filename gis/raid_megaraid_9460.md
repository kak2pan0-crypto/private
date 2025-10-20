# 💾 Собираем RAID в Astra Linux

📝 **Автор**: Грубич В.Е.  
📅 **Дата создания**: декабрь 2024

> 💡 **Пример**: сборка **RAID1** (под ОС) и **RAID6** (под хранилище Кибербэкапа) <br>
> на контроллере **AVAGO MegaRAID SAS 9460-16i**

---

## 🔧 Шаг 1: Вход в BIOS и контроллер RAID

1. Перезагрузите сервер и зайдите в **BIOS**.
   > ⚠️ Если загрузился **NUMA BIOS** — переставьте перемычку на плате.

2. Перейдите:  
   **Advanced → AVAGO MegaRAID → Main Menu → Configuration Management**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nwk6jpxk2d.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/8c0fjyh3le.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ml3o87tr42.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/o709xty7w8.jpg)

---

## 🛠️ Шаг 2: Создание RAID1 под ОС

1. Выберите **Create Virtual Drive**.
2. Укажите:
   - **RAID Level**: `RAID 1`
   - **Disks**: 2 диска с **наименьшим объёмом** (обычно 600 ГБ)
3. Настройте:
   - ✅ Переключите нужные диски в **Enabled**
   - 📌 Имя виртуального диска: `OS`
   - 💾 Нажмите **Apply Changes → OK**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/63936rtxrl.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/w831vn1egn.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/chchx51145.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7lpm3fec17.jpg)

4. Сохраните конфигурацию:
   - **Save Configuration → Confirm → Enabled → Yes → OK**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/l043c0v53k.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/x0ia4d5d2y.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7s6m5ycs6u.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g6ikun71i2.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/of62ay3234.jpg)

---

## 🗃️ Шаг 3: Создание RAID6 под хранилище

1. Повторите шаги выше, но:
   - **RAID Level**: `RAID 6`
   - **Disks**: все диски **кроме одного** (оставьте 1 диск под **Hot Spare**)
2. Задайте имя: `STORAGE` (или другое по проекту)
3. Сохраните конфигурацию аналогично RAID1.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fi253m6es3.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/70x57889b2.jpg)

---

## 🔥 Шаг 4: Настройка Hot Spare

1. Вернитесь в **Main Menu → Drive Management** (`Esc` ×2).
2. Найдите **неконфигурированный диск** (`Unconfigured`).
3. Перейдите:  
   **Operation → Select operation → Assign Dedicated Hot Spare Drive**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ypilu3w36v.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/gvs57hywo0.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4400v95syo.jpg)

4. Назначьте диск **только для RAID6**:
   - Выберите массив → **Enabled → OK**
   - Статус диска изменится на **Hot Spare**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/vjdwir2i8i.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nr78oy4uih.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/icj5x00qeb.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/msg0j0tf40.jpg)

---

## ✅ Шаг 5: Проверка конфигурации

1. Убедитесь, что все RAID созданы:
   - **Main Menu → Configuration Management → View Drive Group Properties**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1ego71j39j.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qri0gcebij.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/6uc3mygoqp.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/k15euscwgq.jpg)

2. Проверьте статус дисков в **Drive Management**:

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bx2r18wmcu.jpg)

---

## ℹ️ Что такое Hot Spare?

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mglj2i3v24.jpg)

> **Hot Spare** — резервный диск, который **автоматически заменяет** вышедший из строя диск в RAID-массиве без участия администратора.

### 📌 Типы Hot Spare:

| Тип | Описание | Плюсы | Минусы |
|-----|----------|-------|--------|
| **Dedicated** | Привязан к **одному RAID-массиву** | Быстрая замена, нет конкуренции | Требует отдельный диск на каждый массив |
| **Global** | Общий для **всех массивов** на контроллере | Экономия дисков | Возможна конкуренция при одновременных сбоях |

---

## 🧠 Наша конфигурация

- **RAID1**: 2 × 600 ГБ (ОС)
- **RAID6**: 8 × 12 ТБ (хранилище)
- **Hot Spare**: 1 × 12 ТБ (**Dedicated** для RAID6)

> ✅ **Почему Dedicated?**  
> RAID6 содержит много дисков → выше вероятность сбоя.  
> RAID1 не требует резерва (2 диска + редкие сбои).  
> Если бы был 3-й диск на 600 ГБ — назначили бы отдельный Hot Spare для RAID1.

---

## 🎉 Завершение

> **На этом всё!**  <br>
> RAID-массивы и Hot Spare успешно настроены. <br>
> 🖥️ **Совет**: Всегда сверяйтесь с **Рабочей Документацией (РД)** проекта — типы RAID и состав дисков могут отличаться. <br>

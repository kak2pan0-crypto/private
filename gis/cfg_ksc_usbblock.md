📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **август 2024**
---

# 🔒 Настройка сервера KSC: блокировка USB через политики

> 📁 **Материалы**:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Касперский\KSC_linux`

---

## 🛠️ Шаг 1: Откройте политику KICS

1. В веб-консоли KSC перейдите в раздел **«Политики»**.
2. Выберите вашу политику:  
   **Kaspersky Industrial CyberSecurity for Linux Nodes 1.3**
3. Перейдите:  
   **«Параметры приложения» → «Продвинутая защита» → «Контроль устройств»**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/8e6cgyebu9.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/u8w2uec6oc.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/i6m8g1349g.jpg)

---

## ⚙️ Шаг 2: Настройка типов устройств

1. Внизу страницы нажмите:  
   **«Настроить параметры для типов устройств»**
2. Найдите раздел **«Съёмные диски»** и установите:  
   **Действие по умолчанию → `Блокировать`**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fclmf2njcr.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/m60752ghs3.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uffiwxwfuq.jpg)

> 💡 Аналогично можно настроить блокировку других типов устройств:
> - Принтеры
> - Bluetooth
> - Wi-Fi адаптеры
> - Портативные устройства и др.

---

## 🚌 Шаг 3: Настройка шин подключения (опционально)

Если требуется более гибкое управление — настройте правила для **шин подключения**:

- **USB**: `Блокировать`
- **FireWire**: `Блокировать` (если не используется)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/i2ufhfw66a.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/llh0wflpge.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/10rwsxm278.jpg)

---

## 🕒 Шаг 4: Настройка расписания (если нужно)

1. Кликните по типу **«Съёмные диски»**.
2. В открывшемся окне задайте расписание:
   - **Расписание**: `Default`
   - **Дни и часы**: `All` (для постоянной блокировки)

> ✅ Если расписание не настроено — правило **не будет применяться**!

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/32t1gdwgt7.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/r1oaolyb7r.jpg)

---

## 🎉 Завершение

> **На этом настройка завершена!**  
> Все USB-накопители теперь **заблокированы** на управляемых АРМах.
> 🚀 **Совет**: После применения политики убедитесь, что она **развернута** на целевые устройства (статус «Применено» в KSC).

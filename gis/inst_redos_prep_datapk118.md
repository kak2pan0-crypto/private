📝 Автор инструкции: **Смирнов В.В.**  
📅 Дата создания: **апрель 2025**

# 🛠️ Установка RedOS и подготовка к настройке DATAPK 1.18

> 📁 **Источник материалов**:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\DATAPK\ПАК DATAPK`

---

## 📥 Установка сертифицированной RedOS 7.3

> ⚠️ Эта версия немного отличается от той, что используется в ITM, и имеет свои особенности.

### 1. 🖥️ Подготовка к установке
- Подключите ISO-образ:  
  `redos-MUROM-7.3-[дата_сборки]-Everything-x86_64-DVD1.iso`

### 2. ⌨️ Раскладка клавиатуры
- Выберите **«английский (Английская (американская))»**
- Переместите её **выше** в списке с помощью кнопки **˄**
- Нажмите **«Готово»**

### 3. 🕒 Дата и время
- Настройте регион и город
- Убедитесь, что дата и время корректны
- Нажмите **«Готово»**

### 4. 📦 Выбор программного обеспечения
- **Базовое окружение**: `Сервер минимальный`
- **Дополнительно**:
  - 🐍 Python
  - 🔗 Библиотеки совместимости

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/egxf5h4ci2.jpg)

### 5. 💾 Разметка диска
- Выберите диск → **«По-своему»** → **«Готово»**
- Нажмите **«Создать их автоматически»**
- Удалите раздел `/home` (кнопка **–**)
- Корневому разделу `/` задайте объём: `99999` → **«Принять изменения»**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/i27mnf732k.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bkjjntypot.jpg)

### 6. 🌐 Настройка сети
- Перейдите в **«Имя сети и узла»**
- Укажите понятное имя узла (например, `DATAPK-01`)
- Нажмите **«Применить»**
- Выберите основной сетевой интерфейс
- Настройте **статический IPv4** → активируйте интерфейс

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5saglhlsaa.jpg)

### 7. 🔐 Пароль root
- Задайте пароль
- ✔️ **Заблокировать учётную запись root**
- ❌ **Снять галочку**: «Разрешить вход root через SSH»

> ⚠️ Осторожно: ошибка в PAM-политиках может привести к потере доступа!

### 8. 👤 Создание пользователя
- Введите имя и пароль
- ✔️ **Сделать этого пользователя администратором**
- Подтвердите пароль

### 9. ▶️ Запуск установки
- Нажмите **«Начать установку»**

---

## ⚙️ Настройка ОС RedOS 7.3 после установки

> 💡 Некоторые шаги (например, настройка NTP) можно пропустить по проекту.

### 1. 🖥️ BIOS/UEFI
- Установите время по **UTC**
- Включите автозапуск после сбоя питания
- Задайте пароль на BIOS

### 2. 🕰️ Настройка аппаратного времени
```bash
sudo timedatectl set-local-rtc 0 --adjust-system-clock
sudo timedatectl set-local-rtc 0
```

### 3. 🔒 Оптимизация SSH
Откройте конфиг:
```bash
sudo nano /etc/ssh/sshd_config
```

Раскомментируйте и задайте:
```ini
GSSAPIAuthentication no
UseDNS no
```

Перезапустите:
```bash
sudo systemctl restart sshd
```

### 4. 🕓 Настройка NTP
```bash
sudo nano /etc/ntp.conf
```
Удалите стандартные серверы, добавьте свои:
```ini
server 10.10.10.10
```

Примените:
```bash
sudo systemctl enable ntpd
sudo systemctl restart ntpd
ntpq -pn  # проверка
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/n3n8f7g7u0.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/t759ylf8fw.jpg)

### 5. 🧠 Настройка ядра (sysctl)
```bash
sudo nano /etc/sysctl.d/99-sysctl.conf
```

Добавьте:
```ini
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
vm.max_map_count=262144
net.core.rmem_default=16777216
net.core.rmem_max=16777216
vm.swappiness = 90
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/huu4p8lgup.jpg)

### 6. 🌐 Включение режима PROMISC
Для интерфейса прослушивания трафика:

```bash
sudo nano /etc/rc.d/rc.local
```

Добавьте:
```sh
#!/bin/sh
ip link set eth1 promisc on  # ← замените eth1 на имя вашего интерфейса
```

Примените права и запустите:
```bash
sudo chmod 744 /etc/rc.d/rc.local
sudo systemctl start rc-local.service
```

Проверка:
```bash
ip a  # интерфейс должен быть в состоянии UP и иметь флаг PROMISC
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nsyekhv1wn.jpg)

### 7. 📡 Настройка сетевых интерфейсов (если не настроены в GUI)

#### Управляющий интерфейс:
```bash
sudo nano /etc/NetworkManager/system-connections/<Имя_управления>
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ml1dwc602t.jpg)

#### Интерфейс прослушивания:
```bash
sudo nano /etc/NetworkManager/system-connections/<Имя_прослушивания>
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/n8jo6nuvke.jpg)

Примените изменения:
```bash
sudo systemctl restart NetworkManager
```

---

## 📦 Подготовка к установке DATAPK

### 1. 📥 Копирование зависимостей
```bash
cd /home/datapk
unzip /home/datapk/dependencies.zip -d /home/datapk
cd dependencies
sudo dnf install --disablerepo=base,updates */* -y
```

### 2. 🐳 Настройка Docker
```bash
sudo mv /usr/bin/docker-compose /usr/local/bin/
sudo chmod +x /usr/local/bin/docker-compose
```

Добавьте `/usr/local/bin` в `secure_path`:
```bash
sudo visudo
```
Найдите строку:
```ini
Defaults secure_path = ...
```
Добавьте в конец: `:/usr/local/bin`

Сохраните (`:wq`).

Запустите Docker:
```bash
sudo systemctl enable --now docker
sudo systemctl status docker  # должен быть active (running)
```

---

## 🔥 (Опционально) Настройка iptables

> ⚠️ Часто **не требуется**, если используется аппаратный межсетевой экран.

```bash
sudo nano /etc/sysconfig/iptables
```

Добавьте правила:
```iptables
-A INPUT -s 172.10.0.0/16 -j ACCEPT
-A INPUT -s 172.8.0.0/24 -p tcp --dport 28428 -j ACCEPT
-A INPUT -p tcp --dport 443 -j ACCEPT
-A INPUT -p udp --dport 514 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 514 -j ACCEPT
-A INPUT -p udp --dport 515 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 515 -j ACCEPT
-A INPUT -p udp --dport 162 -j ACCEPT
-A INPUT -p tcp --dport 25500 -j ACCEPT
```

Примените:
```bash
sudo systemctl enable --now iptables.service
```

---

## 🔄 Финальная перезагрузка

```bash
sudo shutdown -r now
```

После перезагрузки система готова к установке **DATAPK 1.18**.

---

> 🛡️ **Совет**: Всегда сверяйтесь с требованиями проекта — часть настроек (NTP, iptables, IPv6) может быть запрещена или уже реализована на уровне инфраструктуры.

--- 

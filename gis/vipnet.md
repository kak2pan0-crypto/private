# 📚 ViPNet Coordinator HW — Справочник команд

📝 **Автор**: Пьянков М.А.  
📅 **Дата создания**: апрель 2025

> ℹ️ Полный список команд с описаниями и примерами приведён в официальном документе:  
> **«Справочное руководство по командному интерпретатору и конфигурационным файлам»**.

---

## 📖 Содержание

- [🔧 `admin`](#admin)
- [🔄 `alg`](#alg)
- [🔁 `failover`](#failover)
- [🛡️ `firewall`](#firewall)
- [🌐 `inet`](#inet)
- [🔐 `iplir`](#iplir)
- [🖥️ `machine`](#machine)
- [📤 `mftp`](#mftp)
- [⚙️ `service`](#service)
- [🔌 `ups`](#ups)
- [🧩 Прочие команды](#прочие-команды)

---

## 🔧 admin

<a id="admin">admin</a>

Управление конфигурацией, ключами, журналами и аутентификацией.

```text
admin add spare keys                # Добавить РНПК (.pk)
admin authentication-type-token     # Сменить способ аутентификации (пароль/устройство)
admin config list                   # Просмотр сохранённых копий конфигурации
admin config delete ИМЯ_КОПИИ       # Удалить копию конфигурации
admin config load ИМЯ ВЕРСИЯ        # Восстановить конфигурацию
admin config save ИМЯ               # Сохранить текущую конфигурацию
admin escape                        # Перейти в системную оболочку Linux ⚠️ (может снять с гарантии!)
admin export keys binary-encrypted USB  # Экспорт ключей и конфигурации в .vbe
admin export logs usb               # Экспорт системных журналов
admin export-and-clear logs         # Экспорт + очистка журналов
admin export packetdb usb           # Экспорт журнала IP-пакетов
admin kick НОМЕР_СЕССИИ             # Завершить сессию
admin passwd                        # ❌ Не рекомендуется! Меняйте пароль через КЦ ViPNet
admin remove keys                   # Удалить ключи → требуется инициализация
admin show check integrity status   # Проверка целостности файлов
admin ssh show-key                  # Просмотр отпечатков SSH-ключей
admin ssh reset-key X               # Удалить SSH-ключи (X = local / идентификатор / узел)
admin upgrade software usb          # Обновление ПО с USB
```

---

## 🔄 alg

<a id="alg">alg</a>

Управление обработкой прикладных протоколов (ALG — Application Layer Gateway).

```text
alg module ПРОТОКОЛ process off     # Отключить обработку (DNS, FTP, H323, SCCP, SIP)
alg module ПРОТОКОЛ process on      # Включить обработку
alg restart                         # Перезапуск демона algd
alg show                            # Просмотр текущих параметров ALG
```

---

## 🔁 failover

<a id="failover">failover</a>

Настройка кластера горячего резервирования.

```text
failover config edit                # Редактировать конфигурацию
failover show config                # Просмотр конфигурации
failover show info                  # Текущее состояние failover
failover view                       # Журнал переключений
failover config mode РЕЖИМ          # single / cluster
failover show active-mac-address    # MAC активного узла (с пассивного)
failover start                      # Запуск службы
failover stop                       # Остановка службы
```

---

## 🛡️ firewall

<a id="firewall">firewall</a>

Управление сетевыми фильтрами и NAT.  
> ⚠️ Рекомендуется использовать **веб-интерфейс** из-за сложного синтаксиса.

```text
firewall ТИП add ...                # Создать правило (local, forward, tunnel, vpn, nat)
firewall ТИП delete ...             # Удалить правило
firewall ТИП move rule ...          # Изменить приоритет правила
firewall object add name ИМЯ ...    # Создать группу объектов (ip, vpn, interface, service)
firewall object delete ИМЯ          # Удалить группу объектов
firewall object show                # Просмотр всех групп
firewall rules show                 # Просмотр всех правил
```

---

## 🌐 inet

<a id="inet">inet</a>

Управление сетевыми интерфейсами, маршрутизацией, DHCP, DNS, NTP, VLAN, Wi-Fi и т.д.

> 💡 Очень объёмный раздел — ниже только ключевые команды.

```text
inet ifconfig eth0 up/down          # Включить/выключить интерфейс
inet ifconfig eth0 address IP/маска # Назначить IP
inet route add default next-hop IP  # Добавить маршрут по умолчанию
inet dhcp server mode on/off        # Включить/выключить DHCP-сервер
inet dns mode on/off                # Включить/выключить DNS-сервер
inet ntp mode on/off                # Включить/выключить NTP-сервер
inet bonding add ...                # Настройка агрегации интерфейсов
inet vlan НОМЕР add                 # Создать VLAN-интерфейс
inet wifi mode on/off               # Включить/выключить Wi-Fi
inet show interface eth0            # Просмотр состояния интерфейса
inet ping IP                        # Проверка связи (открытая сеть)
```

---

## 🔐 iplir

<a id="iplir">iplir</a>

Управление ядром ViPNet: туннели, шифрование, таблицы ключей, антиспуфинг.

```text
iplir config                        # Редактировать конфигурацию
iplir adapter add eth0 traffic on   # Добавить интерфейс
iplir adapter delete eth0           # Удалить интерфейс
iplir option set antispoofing on    # Включить антиспуфинг
iplir option set max-connections N  # Ограничить число соединений
iplir ping ИДЕНТИФИКАТОР            # Проверка связи с узлом ViPNet
iplir set cipher-mode gost-ctr      # Режим шифрования
iplir show key-info                 # Информация о ключах
iplir show config                   # Просмотр конфигурации
iplir start / stop                  # Запуск / остановка службы
iplir view                          # Журнал регистрации IP-пакетов
```

---

## 🖥️ machine

<a id="machine">machine</a>

Управление системой: перезагрузка, дата, хостнейм, журналы, память.

```text
machine halt                        # Выключить координатор
machine reboot                      # Перезагрузить
machine self-test                   # Регламентное тестирование
machine set hostname ИМЯ            # Задать имя хоста
machine set date ГГГГ-ММ-ДД ЧЧ:ММ:СС # Установить дату и время
machine set timezone Asia/Krasnoyarsk # Задать часовой пояс
machine set session-timeout 30      # Таймаут неактивности (мин)
machine show logs                   # Просмотр системного журнала
machine show memory                 # Использование памяти и swap
machine show uptime                 # Время работы без перезагрузки
machine swap mode on                # Включить swap
machine swap set 2048               # Размер swap = 2 ГБ
```

---

## 📤 mftp

<a id="mftp">mftp</a>

Управление транспортным модулем (MFTP — Mail File Transfer Protocol).

```text
mftp config                         # Редактировать конфигурацию
mftp info                           # Очередь исходящих конвертов
mftp view                           # Журнал MFTP
mftp show config                    # Просмотр конфигурации
mftp start / stop                   # Запуск / остановка службы
```

---

## ⚙️ service

<a id="service">service</a>

Настройка сервисов: HTTP-прокси, антивирусная проверка, фильтрация контента.

```text
service http-proxy mode on          # Включить прокси
service http-proxy antivirus mode on # Включить антивирусную проверку
service http-proxy cache 1024       # Размер кэша = 1 ГБ
service http-proxy content-filter mode on # Включить фильтрацию
service http-proxy listen-address add eth0 8080 # Прослушивать порт
service http-proxy start / stop     # Запуск / остановка прокси
```

---

## 🔌 ups

<a id="ups">ups</a>

Интеграция с источником бесперебойного питания (UPS).

```text
ups set mode master                 # Режим «мастер»
ups set mode slave IP_МАСТЕРА       # Режим «слейв»
ups set monitoring on               # Включить мониторинг UPS
ups show config                     # Просмотр настроек
ups show status                     # Текущее состояние UPS
ups start / stop                    # Запуск / остановка службы NUT
```

---

## 🧩 Прочие команды

<a id="прочие-команды">Прочие команды</a>

```text
# Общие
vpn start / stop                    # Запуск / остановка всех служб ViPNet
webui restart                       # Перезапуск веб-интерфейса
webui status                        # Состояние веб-интерфейса
debug on / off                      # Включить/выключить отладку
enable                              # Перейти в режим администратора
exit                                # Выход из CLI
version                             # Версия ПО
version features list               # Список установленных модулей
who                                 # Активные сессии
```

---

## 🏁 Завершение

> **На этом всё!**  <br>
> Используйте эту шпаргалку для быстрого доступа к командам ViPNet Coordinator HW. <br>
> 👑 **Совет**: Всегда делайте резервную копию конфигурации перед внесением изменений:  <br>
> <br>
> admin config save backup_$(date +%Y%m%d)

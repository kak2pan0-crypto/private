# 🧪 EVE-NG: Настройка рабочего стенда на VMware

📌 **Автор**: Грубич В.Е.  
📅 **Дата создания**: ноябрь 2024  

> 💾 **Образы и материалы**:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\EVE-NG\v6 и образы`  
> (Подходит для макетирования ACS и TACACS+)

---

## 🖥️ Установка EVE-NG на VMware

EVE-NG основан на **Ubuntu** и устанавливается как обычная ОС. Однако требует:

- ✅ Включённой поддержки **Intel VT-x / AMD-V** в BIOS/настройках ВМ
- ✅ **Две сетевые карты** в одной подсети (для связи с другими ВМ, например, с сервером Efros ACS)

> 🎯 **Цель примера**:  
> Виртуализация Cisco-коммутатора + настройка TACACS+ → интеграция с Efros ACS на другой ВМ.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/eqjaxtkgmh.jpg)

---

## ⚙️ Первичная настройка после установки

1. **Стандартные учётные данные**:
   - Логин: `root`
   - Пароль: `eve`

2. При первом входе система предложит:
   - Сменить пароль root (можно оставить `eve`)
   - Указать **имя хоста**
   - Указать **DNS-домен**
   - Настроить **статический IP-адрес**:
     - IP-адрес
     - Маска подсети
     - Шлюз по умолчанию
     - DNS-сервер

3. Прокси: выберите **“direct connection”**

4. После настройки откройте веб-интерфейс:
   - Адрес: `http://<ваш_IP>`
   - Логин: `admin`
   - Пароль: `eve`

---

## 📦 Загрузка образов Cisco IOL

Поместите образы (например, `L2-ADVENTERPRISEK9-M-15.2-20150703.bin`) в:

```bash
/opt/unetlab/addons/iol/bin/
```

Затем **обязательно** выполните:

```bash
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

> ⚠️ **Важно**: эту команду нужно запускать **каждый раз** после добавления новых образов!

---

## 🔑 Генерация лицензии для IOL

Создайте Python-скрипт:

```bash
nano /opt/unetlab/addons/iol/bin/CiscoIOUKeygen3f.py
```

Вставьте содержимое:

```python
#!/usr/bin/python3
print("*")
print("Cisco IOU License Generator - Kal 2011, python port of 2006 C version")
import os
import socket
import hashlib
import struct

# get the host id and host name to calculate the hostkey
hostid = os.popen("hostid").read().strip()
hostname = socket.gethostname()
ioukey = int(hostid, 16)
for x in hostname:
    ioukey = ioukey + ord(x)
print("hostid=" + hostid + ", hostname=" + hostname + ", ioukey=" + hex(ioukey)[2:])

# create the license using md5sum
iouPad1 = b'\x4B\x58\x21\x81\x56\x7B\x0D\xF3\x21\x43\x9B\x7E\xAC\x1D\xE6\x8A'
iouPad2 = b'\x80' + 39 * b'\0'
md5input = iouPad1 + iouPad2 + struct.pack('!i', ioukey) + iouPad1
iouLicense = hashlib.md5(md5input).hexdigest()[:16]

print("\nAdd the following text to ~/.iourc:")
print("[license]\n" + hostname + " = " + iouLicense + ";\n")

with open("/opt/unetlab/addons/iol/bin/iourc", "wt") as out_file:
    out_file.write("[license]\n" + hostname + " = " + iouLicense + ";\n")

print("^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^\nAlready copy to the file iourc\n ")
print("You can disable the phone home feature with something like:")
print(" echo '127.0.0.127 xml.cisco.com' >> /etc/hosts\n")
```

> 💡 **Важно**: Python чувствителен к отступам! Используйте **4 пробела** или **табуляцию**, но не смешивайте.

Сделайте скрипт исполняемым и запустите:

```bash
chmod u+x /opt/unetlab/addons/iol/bin/CiscoIOUKeygen3f.py
/opt/unetlab/addons/iol/bin/CiscoIOUKeygen3f.py
```

Проверьте результат:

```bash
cat /opt/unetlab/addons/iol/bin/iourc
```

Отключите «звонки домой» Cisco:

```bash
echo '127.0.0.127 xml.cisco.com' >> /etc/hosts
```

---

## ➕ Пример добавления устройства

1. В веб-интерфейсе создайте новую лабораторию.
2. Нажмите **«Add a new node»**.
3. Выберите тип устройства (например, **IOL → L2** или **L3**).
4. Укажите имя и выберите нужный образ.
5. Нажмите **Save**.

> ⚠️ **Важно**: чтобы соединить устройства кабелем — **они должны быть выключены**!

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/d0uprxqu6u.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qnyielpcpc.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2dbp3sn7wb.jpg)

Запустите устройство:

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uel636r0au.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5e0pbqmf7p.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/txyqtef7tm.jpg)

Подключитесь через **HTML5 Console**:

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hetvc0dnth.jpg)

> 📌 **Примечание**: команда `show running-config` может обрезаться в консоли.

Чтобы увидеть полный конфиг:
1. ПКМ по устройству → **Export/Import**
2. Слева выберите **Startup-configs**
3. Кликните по устройству → включите **Ace Editor**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/9nb33imsm3.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/c93p2lsxk3.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/x906f2y4i2.jpg)

Теперь вы видите **полный конфиг**:

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ojy356uu4b.jpg)

> 🌐 В примере пинги идут на ВМ с **Efros ACS**, размещённую в той же подсети.

---

## 🎉 На этом всё!

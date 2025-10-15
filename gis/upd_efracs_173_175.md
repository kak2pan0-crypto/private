---
layout: default
title: "Установка Efros ACS на Linux Astra 1.7.3"
description: "Краткая база знаний ПО ГИС и другие рабочие моменты"
---

✏️ Грубич В.Е. — Grubich-V@gaz-is.ru <br>
📆 Дата создания: июнь 2024 <br>

# 🚀 Обновление Efros ACS с Astra 1.7.3 до 1.7.5

## 💿 На момент написания инструкции актуальные сертифицированные образы:

📂 P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Linux\ГДУ\Astra Linux 1.7.5

    🧩 Astra Linux installation-1.7.5.16-06.02.24_14.21.iso
    🧩 Astra Linux base-1.7.5.16-06.02.24_14.21.iso

## 🔄 Первым делом обновимся до 1.7.5
<br>

## 📀Примонтируем два диска, установочный и base в заранее созданные директории /mnt/upd1 и /mnt/upd2


![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/leb6y0xbe8.jpg)

Так же пропишим их в sourse.list

```bash
nano /etc/apt/sources.list
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/p8jh7po5q1.jpg)

Выполним обновление командами

```bash
apt update
astra-update -A -r -T
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/rqkqeyhlka.jpg)

Запуститься обновление с Астра 1.7.3 до 1.7.5 ... ожидаем завершения

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qgwdbkcdc1.jpg)


## 💡 Перезагружаемся...

Теперь настраиваем некоторые параметры, т.к. докер контейнеры у нас сейчас не запустяться.

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2ukml1hl1u.jpg)


## 🧠 Настраиваем значения в `/etc/default/grub`

```bash
nano /etc/default/grub
```

Заменяем полностью две строчки, строчками ниже:

```text
GRUB_CMDLINE_LINUX_DEFAULT="parsec.mac=0 quiet net.ifnames=0"
GRUB_CMDLINE_LINUX="systemd.unified_cgroup_hierarchy=0"
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0g2b65u5mb003.jpg)

Сохраняемся и выполняем

```bash
update-grub
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/diwrpbl6fr003.jpg)


## 🔒 Так же необходимо отключить системный контроль:

```bash
sudo astra-mic-control disable
sudo astra-digsig-control disable
sudo astra-nochmodx-lock disable
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/cibvueia5i003.jpg)


## 🐳 Настройка Докера - отредактировать параметры в nano `/lib/systemd/system/docker.service`

Заменить существующую строку, строкой ниже:

```text
ExecStart=/usr/sbin/dockerd --pidfile=/var/run/docker/docker.pid
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fm4quhpr7x003.jpg)



Дальше отредактировать параметры в `docker.service`

```bash
nano /lib/systemd/system/docker.service
```

Заменить существующую строку, строкой ниже:

```text
ExecStart=/usr/sbin/dockerd --pidfile=/var/run/docker/docker.pid
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fm4quhpr7x003.jpg)

И так же правим в 

```bash
nano /etc/docker/daemon.json
```

Указываем значение

```text
"hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2376"]
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/sgnhfqjxqe003.jpg)

Перезапускаем службы:

```bash
systemctl daemon-reload
systemctl start docker
перезагружаем сервер - reboot
```


## 🧩 И выполняем перереконфигурацию Efros ACS выполнить команду:

```bash
dpkg-reconfigure efros-acs
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xgxncvvmie.jpg)

После перезагрузки и перереконфигурации добавляем новые контейнеры в автозагрузку, заменяя на нужные ID контейнеров из команды docker ps

```bash
docker update --restart=always 1c9a3a6bafcd
docker update --restart=always c0d803e9ab0d
docker update --restart=always 3738a22b0948
docker update --restart=always b14a1d60aa64
```

![0](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/44i44r51tk.jpg)


## ✅ Перезагружаемся и проверяем что докер контейнеры автоматически поднялись `docker ps`


## 🎉 На этом всё 🎉

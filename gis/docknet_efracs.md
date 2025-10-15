---
layout: default
title: "Смена подсети на докер в ACS и не только"
description: "Краткая база знаний ПО ГИС и другие рабочие моменты"
---


✏️ Автор: Тагильцев М.Ю. —  Tagiltsev-M@gaz-is.ru <br>
📆 Дата создания: август 2024 <br>

# 🌐 Смена подсети на докер в ACS

Бывает что сети АСУТП пересекаются с сетями докера и тогда необходимо сменить подсеть докера.
Делаем настройку сети после развёртывания и настройки комплекса, в самую последнюю очередь.

## 🧹 Выполним команды очистки:

```bash
docker rm -f `docker ps -q -a` # Удаление всех контейнеров
docker system prune # Очистка системы Docker
docker network prune # Очистка неиспользуемых сетей
```

## ⚙️ редактируем файл настроек сети докера

```bash
nano /etc/docker/daemon.json
```

добавляем

```text
"bip": "10.1.0.1/17",
"default-address-pools": [{
"base": "10.1.0.0/16",
"size": 17
}],
```

💡 в файле в системе была ещё строчка, её указываем после запятой - `"}],"`  (если сточки нет запятая не нужна, но обычно она есть)

## 🔁 Выполняем рестарт службы <br>

Cоздаём сеть, а так же запускаем перереконфигурацию сети, и прописываем уже новую сеть докера
в нашем случае это 10.1.0.0/16

```bash
systemctl restart docker
docker network create efros-acs-network
dpkg-reconfigure efros-acs
```

## 🧩 Пример конфига с объекта ACS

```text
{
        "tls": true,
        "tlsverify": true,
        "tlscacert": "/var/lib/docker/volumes/efros-acs_docker_ssl/_data/ca.pem",
        "tlscert": "/var/lib/docker/volumes/efros-acs_docker_ssl/_data/server.pem",
        "tlskey": "/var/lib/docker/volumes/efros-acs_docker_ssl/_data/server.key",
    "live-restore": true,
    "bip" : "10.1.0.1/17",
    "default-address-pools": [{
                "base": "10.1.0.0/16",
                "size": 17
        }],
    "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2376"]
}
```

✅ На этом настройка закончена

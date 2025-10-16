📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **июнь 2024**

# 📥 Установка Агента для DatAPK ITM-K

> 📁 **Образы и ПО** доступны на сетевом диске:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\DATAPK\DataPK ITM\Развертывание на РЕД ОС 7.3`

---

## 🛠️ Установка Агента

### Для **Astra Linux**:

```bash
dpkg -i itm-agent_v.1.2.0.debian10_amd64.deb
```

### Для **RedOS / RHEL / CentOS**:

```bash
yum install itm-agent2_v.1.2.0.el8.x86_64.rpm
```

---

## ⚙️ Настройка и запуск агента

В конфигурационном файле `/etc/zabbix/zabbix_agent2.conf` укажите:

```bash
nano /etc/zabbix/zabbix_agent2.conf
```


```ini
Server=192.168.133.139  # ← IP-адрес сервера ITM-K
ListenPort=10050
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xp85fuor4u002.jpg)

После установки выполните:

```bash
systemctl enable zabbix-agent2.service --now
systemctl status zabbix-agent2
nano /etc/zabbix/zabbix_agent2.conf
systemctl restart zabbix-agent2
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/h5ocwg50i4002.jpg)

---

## 🌐 Настройка узла в веб-интерфейсе ITM-K

1. Перейдите в **«Настройка» → «Узлы сети» → «Создать узел сети»**.
2. Укажите:
   - **Имя**: например, `srv-app-01`
   - **Группа узлов**:
     - `Агент Linux` — для серверов и АРМов
     - `Агенты SNMP` — для ИБП, АСО, BMC/iLO
3. Добавьте **интерфейс**:
   - Тип: **Агент**
   - IP-адрес: адрес мониторируемого сервера
   - Порт: `10050`

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/19lxr6y5wm002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uhwjcd6hix002.jpg)

4. Перейдите на вкладку **«Шаблоны»** → выберите **«Linux by Zabbix agent»** → нажмите **«Добавить»**.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/96bibf3hm8002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/pwt6u2bqdc002.jpg)

5. Сохраните узел.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/i5nu8qrl3c002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/o5mofw9126002.jpg)

---

## ✅ Проверка работы

1. Перейдите в **«Мониторинг» → «Узлы сети»**.
2. Найдите ваш сервер — в столбце **«Доступность»** должен гореть **зелёный `ZBX`**.

> ⏳ **Важно**: на реальном оборудовании данные могут появиться **через 3–5 минут**, так как ITM-K опрашивает агенты по расписанию (принудительный опрос недоступен).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/h25t3ms37e002.jpg)

---

## 🖥️ Мониторинг самого ITM-K (самонаблюдение)

Чтобы ITM-K мониторил **сам себя**, в его собственном агенте нужно указать **IP-адрес контейнера Zabbix Server**, а не localhost или внешний IP.

### Как узнать IP контейнера:

1. Найдите имя сети:

```bash
docker network list
```

# Ищем: datapkitm_itm_k_network

2. Получите детали сети:

   ```bash
   docker network inspect datapkitm_itm_k_network
   ```

3. Найдите контейнер `zabbix-server` и его IP (например, `172.10.0.1`).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qkvu0ye6tj002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/m1n96l7a4j002.jpg)

4. В файле **`/etc/zabbix/zabbix_agent2.conf` на сервере ITM-K** укажите:

   ```ini
   Server=172.10.0.1
   ```

5. В веб-интерфейсе при создании узла для ITM-K укажите **его реальный внешний IP** (тот, по которому к нему обращаются другие системы).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g06r6432e5002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/42p3i36mca002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5pl2lpy1fg002.jpg)

---

## 🎉 Завершение

> **На этом всё!**  
> Агент установлен, настроен и передаёт данные в ITM-K.


Готово! Этот документ можно сразу использовать в GitHub, GitLab или любой Markdown-совместимой системе.

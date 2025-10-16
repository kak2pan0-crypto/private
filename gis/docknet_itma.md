📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **май 2025**
  
# 🌐 Смена Docker-сети на DatAPK ITM-A

> 💡 **По сути всё просто**: остановили контейнеры → удалили старую сеть → запустили с новыми IP через `env_generator.sh`.


## 📌 Зачем это нужно?

Если ITM-A был развёрнут со стандартной Docker-сетью (`172.17.0.0/16`), контейнеры получают IP-адреса вроде `172.16.240.2` и `172.16.240.3`.  
Это может **конфликтовать** с другими системами или АСУТП.  
Решение — сменить подсеть на уникальную, например: `10.10.0.0/16`.

---

## 🔍 Шаг 1: Проверка текущей сети

Перед изменением убедитесь, какие IP используют контейнеры:

```bash
docker ps  # узнаём имена контейнеров
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' itm_a_zabbix_snmptraps
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' itm_a_zabbix_proxy
docker network inspect datapkitm_itm_a_network
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ey9qibkcam.jpg)

---

## ⏹️ Шаг 2: Остановка и очистка

Перейдите в директорию с compose-файлом (обычно `/opt/datapkitm`) и остановите всё:

```bash
cd /opt/datapkitm
docker-compose down
rm -rf ./env && rm -f ./.env
```

> 🗑️ Удаление `.env` и папки `env` гарантирует, что при следующем запуске будут применены **новые настройки**.

## 🔄 Шаг 3: Генерация новой конфигурации

Запустите генератор с новыми параметрами сети:

```bash
chmod +x env_generator.sh
./env_generator.sh
```

При запуске укажите:
- IP-адрес сервера
- Порт БД (`5432`)
- **Новую подсеть**, например: `10.10.0.0/16`

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/s6ahbplaki.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1qubjwcd0r.jpg)

## ✅ Шаг 4: Проверка новых IP

После запуска убедитесь, что контейнеры получили новые адреса:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' itm_a_zabbix_snmptraps
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' itm_a_zabbix_proxy
docker network inspect datapkitm_itm_a_network
```

## 🛠️ Дополнительно: Смена **глобальной** сети Docker

> ⚠️ **Важно!** По умолчанию Docker создаёт сеть `172.17.0.0/16`. Чтобы избежать конфликтов **в будущем**, измените её на уровне демона.

1. Создайте или отредактируйте файл:
   
   ```bash
   nano /etc/docker/daemon.json
   ```

3. Вставьте конфигурацию (пример для `10.10.0.0/16`):

   ```json
   {
     "bip": "10.10.0.1/16",
     "default-address-pools": [
       {
         "base": "10.10.0.0/16",
         "size": 24
       }
     ]
   }
   ```

   Или выполните одной командой:

   ```bash
   sudo tee /etc/docker/daemon.json <<EOF
   {
     "bip": "10.10.0.1/16",
     "default-address-pools": [
       {
         "base": "10.10.0.0/16",
         "size": 24
       }
     ]
   }
   EOF
   ```

4. Перезапустите Docker:
   
   ```bash
   systemctl restart docker
   ```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/vo87bf0hpl.jpg)

> 🔒 Убедитесь, что выбранная подсеть **не пересекается** с сетями заказчика, АСУТП или других систем!

## 🧪 Финальная проверка

- Перезагрузите сервер, чтобы убедиться, что всё поднимается автоматически:
  
  ```bash
  reboot
  ```
  
- После загрузки проверьте:
  
  ```bash
  docker ps
  docker network inspect datapkitm_itm_a_network
  ```

## 🎉 Завершение

> **На этом всё!**  
> Сеть ITM-A успешно изменена и больше не конфликтует с инфраструктурой.
> 🐳 **Совет**: Всегда планируйте сетевую схему **до установки**, чтобы избежать подобных манипуляций в будущем.

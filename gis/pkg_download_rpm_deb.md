# 📥 Скачать пакет без установки (для offline-установки)

📝 **Автор**: Грубич В.Е.  
📅 **Дата создания**: август 2024  

> 💡 **Сценарий**:  
> Нужно установить пакет (например, PostgreSQL) на сервер **без интернета**.  
> Решение: скачать пакет **со всеми зависимостями** на виртуальную машину с такой же ОС и интернетом, затем перенести архивы на целевой сервер.

---

## 🔧 Пример для Red OS

Команда для скачивания пакета **без установки**:

```bash
yum install --downloadonly postgresql15-server
```

> 📁 Пакеты сохраняются в:  
> `/var/cache/dnf/updates-3a6a0996c397f44b/packages`  
> или в других подкаталогах `/var/cache/dnf/` — ищите по имени репозитория.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/9r7ypfv9ln.jpg)

> ⚠️ **Важно**: после установки из `/var/cache/...` пакеты **удаляются**!  
> Поэтому сразу скопируйте их в отдельную папку:

```bash
cp /var/cache/dnf/*/packages/*.rpm ./offline-pkgs/
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ndhdrpil7c.jpg)

### 📦 Указание собственной директории (опционально)

Можно сразу сохранить всё в нужную папку и скачать несколько пакетов:

```bash
yum install --downloadonly --downloaddir=/tmp/postgres-offline \
  postgresql15-server \
  postgresql15-contrib \
  postgresql15-libs
```

> 💡 Символ `\` — это перенос команды на новую строку для удобства чтения.

---

## 🐧 Пример для Astra Linux

1. **Раскомментируйте интернет-репозитории**:
   ```bash
   nano /etc/apt/sources.list
   ```
   Закомментируйте локальные (`file://`) и раскомментируйте онлайн-репозитории.

2. Обновите список пакетов:
   ```bash
   apt update
   ```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7syatfaq8y.jpg)

3. Скачайте пакет **без установки**:
   ```bash
   apt download postgresql
   # или для всех зависимостей:
   apt install --download-only postgresql
   ```

> 📁 Пакеты сохраняются в:  
> `/var/cache/apt/archives/`

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/64g42ymjx9.jpg)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5tlp7fqit5.jpg)

Проверим содержимое:

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ngucb05f93.jpg)

> 💡 В примере установлена **PostgreSQL 11**, т.к. версия 15 отсутствует в репозитории Astra.  
> Для других версий можно использовать **Debian** или подключить сторонние репозитории.

---

## 🚚 Перенос на целевой сервер

1. Скопируйте все `.rpm` (Red OS) или `.deb` (Astra) файлы на сервер без интернета.
2. Установите вручную:

### Для Red OS:
```bash
yum localinstall *.rpm
# или
dnf install *.rpm
```

### Для Astra:
```bash
dpkg -i *.deb
apt --fix-broken install -y  # если есть недостающие зависимости
```

---

## 🎉 На этом всё!

--- 

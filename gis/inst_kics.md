
### 📝 Автор инструкции
- **Грубич В.Е.**  
- 📧 Email: `Grubich-V@gaz-is.ru`  
- 📅 Дата создания: **июнь 2024**

# 🛡️ Установка KICS (Kaspersky Industrial CyberSecurity)

> 📁 **Важно**: Почти весь официальный сайт KICS доступен **офлайн** на сетевом диске:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Касперский\Выкачанный сайт KICS Linux 1.3`

Также там находятся:
- 📦 Сам дистрибутив KICS
- 📥 Базы обновлений
- 📘 Руководства (в т.ч. `Руководство по эксплуатации KICS Linux Nodes 1.3.pdf`)

> ⚠️ Для **тестирования** требуется **триальный ключ** и **доступ в интернет** (даже на виртуальной машине).

---

## ⚠️ Внимание!

> 🔴 **Без обновлённых антивирусных баз KICS не запускается и не работает!**  
> Настройка возможна **только после успешного обновления**.

Готовые базы можно взять здесь:  
`P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Касперский`

Инструкция по скачиванию обновлений: [тут](./download_upd.html)

---

## 📥 Установка консольной версии

> 💡 **Важно**: Даже если нужен графический интерфейс — **сначала устанавливайте консольную версию**!

1. Перейдите в директорию с пакетом.
2. Выполните одну из команд:

```bash
# Через apt (рекомендуется)
sudo apt-get install ./kics_1.3.0-1206_amd64.deb

# Или через dpkg
sudo dpkg -i kics_1.3.0-1206_amd64.deb
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/23up88kb1n003.jpg)

---

## ⚙️ Настройка через скрипт

Запустите конфигурационный скрипт:

```bash
/opt/kaspersky/kics/bin/kics-setup.pl
```

### Шаги настройки:
1. **Язык** → выберите нужный.
2. **Лицензионное соглашение** → прокрутите до конца (лучше `PgDown`) → подтвердите **трижды**.
3. **Пользователь администратора** → укажите пользователя с правами sudo (например, `gis`).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hftqxd6j6w003.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hjfjc7ee4l003.jpg)

> ✅ **Обязательно**: укажите пользователя с админскими правами!

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0l6r7v40kn003.jpg)

---

## 📥 Источник обновлений

На этом шаге укажите **источник антивирусных баз**.

### Варианты:
- **KLServers** — онлайн-обновление с серверов «Лаборатории Касперского»
- **SCServer** — через Kaspersky Security Center
- **`<адрес>`** — пользовательский URL (локальный или интернет)
- **`<путь>`** — локальная директория с автономными базами

### Для автономного обновления:
Укажите путь к каталогу с базами, например:  
`/srv/update_kics`

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mu7kvcirni003.jpg)

> 💡 Пример структуры: базы должны лежать в указанной папке в правильном формате.

---

## 🔄 Обновление и завершение настройки

1. Система предложит **обновиться сразу** — соглашайтесь.
2. Убедитесь, что путь к базам указан **точно** (проверьте регистр и слэши!).
3. **Прокси не настраиваем** (если не требуется).
4. **Расписание обновлений** — отключите, если нет KSC-сервера.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1xxwsvpd1l003.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/dbv7gtm9bu003.jpg)

5. Укажите **полный путь к файлу лицензии**, включая имя файла и расширение (`.lic`).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4ufygu13n7003.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3pj4k57l92003.jpg)

---

## 🖥️ Установка графического интерфейса

После настройки консольной версии установите GUI:

```bash
sudo apt-get install ./kics-gui_1.3.0-1206_amd64.deb
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/f5b70qafmh003.jpg)

---

## ✅ Завершение

> 🎉 Установка завершена!

- Запустите KICS через меню **«Системные»**.
- **Настройки станут доступны только после активации лицензии и обновления баз**.
- В графическом интерфейсе можно:
  - Включать/отключать компоненты защиты
  - Просматривать журнал событий
  - Но **нельзя** менять глубокие параметры — они задаются только в консоли или через KSC.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/y0kxyx0bq8003.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/n8upu90aaa003.jpg)

---

## 🗑️ Удаление (на будущее)

### Для DEB-пакетов (Debian/Ubuntu/Astra):
```bash
sudo apt-get purge kics kics-gui
```

### Для RPM-пакетов (RedOS/RHEL):
```bash
sudo rpm -e kics kics-gui
```

---

## 🏁 Завершение

> **На этом сама установка закончена.**

---

> 🦎 **Совет**: Всегда проверяйте версию дистрибутива и совместимость с вашей ОС. KICS 1.3 работает только на сертифицированных платформах!

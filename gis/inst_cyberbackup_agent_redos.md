# 💾 Установка агента Cyber Backup на RedOS

📝 **Автор**: Грубич В.Е.  
📅 **Дата создания**: декабрь 2024

---

## 📁 Источники

- **Локальный диск**:  
  `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Acronis`
- **Официальный сайт**:  
  🔗 [https://cyberprotect.ru/updates/updates-backup/](https://cyberprotect.ru/updates/updates-backup/)

> 💡 В примере используется:  
> `CyberBackup_16.5_64-bit_29856.x86_64`  
> на **RedOS 6.1.52-1.el7.3.x86_64**

---

## ⚠️ Проблема

При установке агента Cyber Backup на RedOS может возникнуть ошибка, связанная с отсутствием или несовместимостью модуля **SnapAPI** (требуется для блочного резервного копирования).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/lfpmle89nv.jpg)

> 📚 Подробнее: [https://kb.cyberprotect.ru/articles/snapapi-manual](https://kb.cyberprotect.ru/articles/snapapi-manual)

---

## 📥 Шаг 1: Подготовка пакетов (на машине с интернетом)

Если сервер **без доступа в интернет**, скачайте зависимости на другой машине:

```bash
# Скачиваем пакеты в /tmp
yum install --downloadonly --downloaddir=/tmp/kernel_update \
  kernel-lt-6.1.110-1.el7.3 \
  kernel-lt-devel-6.1.110-1.el7.3 \
  kernel-lt-headers-6.1.110-1.el7.3 \
  kernel-lt-tools-6.1.110-1.el7.3 \
  kernel-lt-tools-libs-6.1.110-1.el7.3

# Дополнительные зависимости
yum install --downloadonly gcc
yum install --downloadonly make
yum install --downloadonly redos-kernels-release
yum update --downloadonly kernel
yum update --downloadonly rpm
yum update --downloadonly dkms
yum update --downloadonly dpkg
yum update --downloadonly kernel-devel
```

> 💾 **Готовые пакеты** уже есть на сетевом диске:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Acronis\Пакеты для установки агента на Ред ОС`

---

## 🔧 Шаг 2: Установка зависимостей (на целевом сервере)

1. Скопируйте пакеты на сервер в папку, например: `/root/packages_cyber`
2. Проверьте версию ядра:
   ```bash
   uname -r
   ```
   → Должно быть: `6.1.52-1.el7.3.x86_64`

3. Установите все пакеты:
   ```bash
   cd /root/packages_cyber
   yum install --disablerepo=base,updates,kernels6 *.rpm --skip-broken
   ```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bi0ki193yf.jpg)

---

## 🔄 Шаг 3: Перезагрузка

После установки **обязательно перезагрузите сервер**:

```bash
reboot
```

После перезагрузки убедитесь, что установлено ядро `kernel-lt`:

```bash
yum list installed | grep kernel-lt
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/sfocuqqt6e.jpg)

---

## ▶️ Шаг 4: Повторная установка агента

Запустите установку **второй раз**:

```bash
chmod +x CyberBackup_16.5_64-bit_29856.x86_64
./CyberBackup_16.5_64-bit_29856.x86_64
```

> ✅ Теперь установка должна завершиться **без ошибок**!

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3us7f9ct1f.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1oniq8jl05.jpg)

---

## 🎉 Завершение

> **На этом установка агента Cyber Backup завершена!**  <br>
> Агент готов к работе в составе системы резервного копирования. <br>
> 🔐 **Совет**: Всегда проверяйте совместимость версий ядра и заголовочных файлов <br>
> это основная причина ошибок при сборке SnapAPI.

### 📝 Автор инструкции
- **Грубич В.Е.**  
- 📧 Email: `Grubich-V@gaz-is.ru`  
- 📅 Дата создания: **июнь 2024**
---

# 📥 Как скачать обновления с официального сайта Касперского

> 💡 **Совет**: Удобнее выполнять эти действия на **Windows-машине**, так как утилита обновлений работает только под Windows.

---

## 1. 📦 Скачивание утилиты обновлений

Перейдите на официальную страницу:  
🔗 [https://support.kaspersky.ru/kuu4-for-windows](https://support.kaspersky.ru/kuu4-for-windows)

Скачайте архив с **Kaspersky Update Utility (KUU)**.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/h42vyfoe8d003.jpg)

---

## 2. ▶️ Запуск утилиты

1. Распакуйте архив — **установка не требуется**.
2. Запустите файл:  
   ```bash
   UpdateUtility-Gui.exe
   ```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bgf11cfkla003.jpg)

---

## 3. 🎯 Выбор продукта и версии

В интерфейсе утилиты:

- Выберите нужный продукт:  
  **Kaspersky Industrial CyberSecurity for Linux Nodes**
- Убедитесь, что выбрана **правильная версия** (в нашем примере — **v1.3**).

> ⚠️ **Важно**: Версия должна **точно совпадать** с установленной на сервере!

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ntc96kq6kv003.jpg)

---

## 4. 📁 Настройка папок для обновлений

1. Перейдите в **«Настройки»** → **«Папки»**.
2. Укажите:
   - **Временную папку** (например, `C:\kics_updates\tmp`)
   - **Основную папку обновлений** (например, `C:\kics_updates\updates`)
3. Обе папки **должны существовать** — создайте их заранее.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0uhjiereur003.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hfgf3q8exf003.jpg)

---

## 5. ⏳ Загрузка обновлений

Нажмите **«Загрузить»**.

- ⏱️ Процесс занимает **30–60 минут** (в зависимости от скорости интернета).
- После завершения — **вся структура обновлений** будет в указанной вами **основной папке**.

> 📤 Для установки на Linux-сервер:
> - Скопируйте **всю папку обновлений** (например, `updates`) на сервер.
> - Укажите **полный путь к этой папке** при настройке KICS (в скрипте `kics-setup.pl`).

---

## ✅ Завершение

> **На этом всё!**  
> Обновления готовы к использованию в автономной среде.

---

> 🦕 **Совет**: Храните скачанные обновления в архиве — они пригодятся при развёртывании новых инсталляций KICS без интернета.

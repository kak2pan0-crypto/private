📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **август 2024**

---

# ⚙️ Настройка KSC 15 на РЕД ОС с KICS: обновления и политики

> 📁 **Материалы**:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Касперский\KSC_linux`

---

## 🔌 Шаг 1: Установка плагина для управления KICS

1. Распакуйте архив:  
   `kics_linux_1_3_local_1.3.0.187.zip`
2. Внутри вы найдёте два файла:
   - `plugin.zip`
   - `signature.txt`
3. В веб-консоли KSC перейдите:  
   **«Управление» → «Плагины» → «Добавить»**
4. Загрузите оба файла и нажмите **«Добавить»**.

✅ После успешной установки плагин появится в списке.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ksc15web.png)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/9ja4ngqqk7.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fl0p9tf7ap.jpg)

---

## 📄 Шаг 2: Создание профиля плагина

1. Перейдите: **«Политики» → «Создать политику»**
2. Выберите тип: **«Kaspersky Industrial CyberSecurity for Linux Nodes 1.3»**
3. Примите лицензионное соглашение.
4. Сохраните профиль (настройки можно изменить позже).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/47r0tytpqe.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/m3yuvcvrb1.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qt6l5ioprl.jpg)

---

## 🔄 Шаг 3: Настройка задачи обновления KICS

1. Перейдите: **«Задачи» → «Kaspersky Industrial CyberSecurity for Linux Nodes 1.3» → «Обновление»**
2. Откройте вкладку **«Параметры приложения»**
3. Укажите:
   - **Источник обновлений**: `Сервер администрирования`
   - **Действие**: `Загружать и устанавливать`
4. Настройте расписание и **сохраните**.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/y1nlt93lq2.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hii1pwr1y4.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/kk9lnet52c.jpg)

---

## 📥 Шаг 4: Указание локального хранилища обновлений

1. Перейдите: **«Задачи» → «Загрузка обновлений в хранилище...»**
2. Во вкладке **«Параметры приложения»** укажите путь к папке с обновлениями (например, `/srv/KICS_Update`).
3. Если обновления скачаны через **Kaspersky Update Utility**, поставьте галочку:  
   **«Обновления получены с помощью утилиты загрузки обновлений»**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0jggpatkhn.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7ujusrtxlp.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5t2b611ms5.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/j7d082l4iu.jpg)

---

## 📊 Шаг 5: Добавление виджета мониторинга

Для удобства добавьте виджет обновлений на панель мониторинга:

1. Нажмите **«+»** на главной панели
2. Выберите: **«Обновления» → «Статус обновлений устройств»**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ebs8wr4jlj.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/dhf4c19mf9.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/jb05q3klkw.jpg)

---

## 🖥️ Шаг 6: Обнаружение устройств с KICS

1. Перейдите: **«Обнаружение устройств и развёртывание»**
2. Создайте **IP-диапазон** или укажите подсеть.
3. В свойствах диапазона **разрешите опрос** — начнётся автоматическое сканирование.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ako7rvmfqd.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7h328hixqa.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/sb8jlhmp83.jpg)

4. После завершения сканирования перейдите в:  
   **«Нераспределённые устройства»**
5. Выберите нужные АРМы и добавьте их в группу **«Управляемые устройства»**

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/wh0otg99v3.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/yvy0gjb1o7.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/me1415wyup.jpg)

6. Убедитесь, что политика применена:  
   - Кликните по имени устройства  
   - Проверьте статус политики

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qxq37kjsd9.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/m02ljuc1sd.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3qb1icg8ij.jpg)

---

## 🚀 Шаг 7: Запуск обновлений

1. Вручную запустите задачу:  
   **«Загрузка обновлений в хранилище Сервера администрирования»**
2. После её завершения запустите:  
   **«Обновление»** в разделе **KICS 1.3**

> ⏳ Обновление может занять несколько минут.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/e4m0i6te4y.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/wv6348cr04.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/a4v7gqn78c.jpg)

3. Следите за статусом в мониторинге:
   - Иногда обновления завершаются с **предупреждениями**
   - Кликните на запись — там будет описание (часто требуется **перезапуск KICS** на АРМе)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fcg9jm63s4.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7s78m4tgo4.jpg)

---

## 🎉 Завершение

> **На этом настройка KSC и KICS завершена!**  <br>
> Все управляемые устройства получают обновления и подчиняются политикам. <br>
> 🌿 **Совет**: Регулярно проверяйте статус обновлений и логи — это поможет избежать проблем с защитой на промышленных объектах.

---
layout: default
title: "Установка Efros CI агента на Windows"
description: "Инструкция по установке агента Efros CI в Windows"
---

# Установка Efros CI агента на Windows

✏️ **Автор инструкции:** Тагильцев М.Ю. <br>
✒️ **Дополнено:** Грубич В.Е.  <br>
📆 **Дата создания:** декабрь 2024  <br> 
🔖 **Дата изменения:** июнь 2025   <br>

---

## Образы и ПО, использованные в данной инструкции

Доступны на сетевом диске:

- [P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Windows](\\gis.lan\dfs\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Windows)  
- [P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Документация](\\gis.lan\dfs\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Документация)

---

## 1. Установка

Запускаем **установку под администратором**.

![Установка агента](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qm8bob7w0s.jpg)
![Папка установки](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1ucukj4q1y.jpg)
![Процесс установки](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mv5hc9icr7.jpg)

После установки переходим в каталог:

```

C:\Program Files\EFROS Config Inspector 4\Agent

```

Запускаем **WASetup.exe**

На вкладке **Дополнительно** указываем адрес сервера (1).  
Если сервер настроен по умолчанию, **снимаем галочки 2 и 3.**

> ⚠️ Если оставить галочку 2, то при попытке соединения сервер включит её автоматически, и возникнет ошибка проверки сертификата.

![WASetup параметры](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7b0npc1l8u.jpg)
![WASetup окно 2](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/syw8ftgjo1.jpg)
![Дополнительные настройки](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0fq645t97h.jpg)

---

## 2. Возможные ошибки при установке

:red_circle: **Ошибка 1 — Windows 7**  
> Не может быть установлен в версиях Windows 7x64, 6.1 (сборка 7600)

:red_circle: **Ошибка 2 — Windows 8**  
> Отсутствует библиотека `api-ms-win-crt-runtime-l1-1-0.dll`

![Пример ошибки](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4n1tm8paqh.jpg)

---

## 3. Использование портативной версии

Для этих случаев используется **портативная версия агента**:

📁  
[`P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Windows\Portable Agent`](\\gis.lan\dfs\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Windows\Portable Agent)

- Для Windows 7 и ниже:  
  `Efros Config Inspector Portable Agent 4 Windows 2000, 2003, 2008, XP, Vista, 7`
- Для Windows 8 и выше (ошибка библиотеки):  
  `Efros Config Inspector Portable Agent 4.x86`

:link: Подробнее — [Инструкция по настройке работы EFROS CI 4 с Windows portable agent (PDF)](file://gis.lan/dfs/Филиал%20г.%20Н.%20Уренгой/12.ОПНР/Обучение/Вендоры/Efros/Efros%20CI/EfrosCI%204.10.103.270%20Сертифицированная/Диск_1_установочный/Windows/Portable%20Agent/Инструкция%20по%20настройке%20работы%20EFROS%20CI%204%20с%20Windows%20portable%20agent.pdf)

---

## 4. Автоматический запуск агента

Чтобы **портативный агент запускался автоматически**, создаём задачу в **планировщике Windows**.

Заполняем поля, как показано на скриншотах:

![Создание задачи](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mh6asvpvi6.jpg)
![Параметры задачи](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/41uml48854.jpg)

Создаём несколько триггеров:

- при запуске ПК  
- при входе в систему  
- по расписанию (каждый день)

![Пример триггеров](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/9vym5xchep.jpg)
![Дополнительный триггер](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mwwwt8gv7p.jpg)

---

## 5. Действия и параметры

В разделе **Действия** добавляем путь к `exe` файлу нашего агента:

![Добавляем exe](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/raaxuypjog.jpg)

Вкладку **Условия** не изменяем.  
В разделе **Параметры** выставляем значения как на скриншоте:

![Параметры запуска](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/14hcgd9kh9.jpg)

---

## 6. Завершение

Настройки автозапуска можно скорректировать под конкретное устройство,  
но в целом приведённых параметров достаточно для стабильного старта агента.

---

# ✅ Установка завершена

На этом установка агента **Efros CI** на Windows завершена.

[Вернуться в оглавление](./efros.html)

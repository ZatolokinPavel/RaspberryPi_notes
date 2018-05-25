# Raspberry Pi notes
_Заметки и инструкции по настройке Raspberry Pi_

## План
1. Установить операционную систему Raspbian Jessie Lite. А именно: скачать с [офф. сайта](https://www.raspberrypi.org/downloads/raspbian/), записать на флешку с помощью **Win32DiskImager**, запустить на малине. Логин/пароль по умолчанию `pi` `raspberry`.
2. Настроить раскладку клавиатуры и язык системы. [(см. ниже)](#Раскладка-клавиатуры-и-локализация-консоли)
3. Провести начальную настройку через утилиту `sudo raspi-config`. [(см. ниже)](#Начальная-настройка)
4. Отключить скринсейвер. [(см. ниже)](#Отключение-скринсейвера)
5. Обновить всё что только можно [(см. ниже)](#Обновление)
6. Установка необходимого минимума программ [(см. ниже)](#Установка-необходимого-минимума-программ)
7. Хорошо бы настроить пользователей системы, но пока не знаю как.
8. [Настроить SSH доступ и локальную сеть.](SSH.md)
9. [Установить и настроить Samba для создания общей папки.](Samba.md)
10. Добавить репозиторий **stretch**. [(см. ниже)](#Добавление-репозитория-stretch)
11. [Настроить часы реального времени.](RTC.md)
12. [Установить и настроить Nginx.](Nginx.md)
13. [Настроить SSL для Nginx (https).](SSL%20(https).md)
14. [Скачать сайт okfilm.com.ua и подключить его к Nginx.](https://github.com/ZatolokinPavel/okfilm)
15. [Установить и настроить Erlang.](Erlang.md)
16. [Скачать и запустить backend на эрланге.](https://github.com/ZatolokinPavel/raspberry_server)
17. [Поднять FTP сервер](FTP.md) для загрузки файлов в общую папку.
17. [Настроить систему проксирования на основе Tor.](TOR%20proxy.md)
18. При желании, [включить порт i2c0 на доп. клеммнике P5](Port%20I2C-0.md)
19. Если надо, установить [Apache, PHP и MySQL](PHP.md)

### Раскладка клавиатуры и локализация консоли
Взято отсюда http://blackdiver.net/it/linux/777  
Заходим в утилиту настройки  
`sudo raspi-config`  
Настраиваем раскладку клавиатуры:
1. Выбрать пункт `4 Localisation Options`
2. Выбрать пункт `I3 Change Keyboard Layout`
3. Далее ждём загрузки клавиатур и выбираем `Generic 105-key (Intl) PC` (или какая больше подходит)
4. На следующем экране выбрать `Other` для перехода в список всех раскладок
5. Выбрать страну — `Russian`
6. Выбрать русскую раскладку — просто `Russian`
7. Теперь выбираем метод переключения раскладок — предпочитаю `Control+Shift`
8. Клавиша временного переключения на латинскую раскладку — `No temporary switch`
9. Клавиша, которая будет использоваться в качестве AltGr — `The default for keyboard layout`
10. Клавиша, которая будет использоваться в качестве Compose key — `No compose key`
11. Использовать Control+Alt+Backspace для остановки X server — Yes (У меня такого не было).

Смена локализации:
1. Выбрать пункт `4 Localisation Options`
2. Выбрать пункт `I1 Change Locale`
3. Выбираем локализации `en_GB.UTF-8 UTF-8` и `ru_RU.UTF-8 UTF-8`  
   _Включение и выключение производится пробелом._
4. Далее нам предложат выбрать локализацию по умолчанию. Выбираем `ru_RU.UTF-8`

После выставления всех настроек нажимаем **Finish**. Raspberry предложит перезагрузиться. Соглашаемся.  
После перезагрузки логинимся в систему, и понимаем, что вместо русских символов в консоли печатаются квадратики. Чтобы такого не было выполняем команду:  
`sudo dpkg-reconfigure console-setup`  
Далее выбираем (увы, но на данном этапе вместо русских букв, скорее всего, будут квадратики, поэтому указываю пункт в меню):
* Используемая кодировка в консоли: UTF-8
* Используемая таблица символов: Определение оптимального набора символов (последний пункт)
* Консольный шрифт: Fixed или Позволить системе выбрать подходящий шрифт (последний пункт)
* Размер шрифта: 8×16

Теперь Raspberry Pi полностью поддерживает русский язык.


### Начальная настройка
Запустить программу начальной настройки  
`sudo raspi-config`  
В окне настройки в принципе почти всё понятно. Есть только несколько рекомендаций.
* **Expand Filesystem** – здесь нужно увеличить root размер на весь размер карты памяти. Нужно сделать в первую очередь.
* **Internationalization Options** – здесь нужно будет установить локаль, часовой пояс и настроить клавиатуру. Об этом отдельно.
* **Advanced Options / Memory Split** – распределение памяти Raspberry Pi. Вам необходимо определиться, сколько оперативной памяти вы готовы выделить для графического процессора. При работе в консоли будет достаточно и 16 Мб, а вот для просмотра видео в графической оболочке придется пожертвовать 64-128 Мб. Выбранные значения могут быть только: 16, 32, 64, 128 или 256.
* **Advanced Options / SSH** – включение или выключение SSH сервера. Рекомендую вам включить SSH, если вы собираетесь использовать удаленное управление.

Остальное не описываю. После завершения настроек нажмите на клавиатуре Ctrl+F, выберите \<Finish\>. Raspberry Pi уйдет на перезагрузку для внесения изменений.

### Установка пароля пользователю 'root' в Raspberry Pi
Набрать в консоли команду `sudo passwd root` и ввести пароль дважды.

### Отключение скринсейвера
При подключении монитора напрямую (не по SSH) если долго не нажимать ничего на клавиатуре, то монитор становится черным. Это мешает и надо отключить. Для этого нужно изменить настройки в файле /etc/kbd/config  
`$ sudo vim.tiny /etc/kbd/config` (а нормального **vim**'а ещё и нету, как и **mc**)
```
BLANK_TIME=0
POWERDOWN_TIME=0
BLANK_DPMS=off
LEDS=+num            # заодно включить по умолчанию Num Lock
DO_VCSTIME=yes       # заодно отображать часы в верхнем правом углу консоли
```
`sudo reboot`

### Обновление
https://www.raspberrypi.org/documentation/raspbian/updating.md  
Нужно обновить все программы  
`$ sudo apt-get update` – обновить информацию о пакетах  
`$ sudo apt-get dist-upgrade`  
`$ sudo apt-get upgrade` – обновление всех установленных пакетов  
Затем обновить прошивку  
`$ sudo apt-get install rpi-update` – программы обновления прошивки может и не быть  
`$ sudo rpi-update` – обновление прошивки ([описание программы](https://github.com/Hexxeh/rpi-update))  
`$ sudo reboot` – теперь надо перезагрузить  
Чистка  
`$ sudo apt-get clean` – после всех обновлений или установок можно чистить кэш установщика  
`$ sudo apt-get autoremove` – удалить неудалённые зависимости от уже удалённых пакетов  
`$ sudo apt-get autoclean` – почистить пакеты .deb которые больше не используются

### Установка необходимого минимума программ
`sudo apt-get install vim`  
`sudo apt-get install mc`  
`sudo apt-get install git`  
`sudo apt-get install htop`  
`sudo apt-get install ntpstat` - статус NTP, нужен для мониторилки сайта  

### Добавление репозитория stretch
Этот репозиторий нужно добавить, чтобы из него ставить самые свежие версии пакетов с помощью apt-get. Это репозиторий будущей версии Debian. А текущая версия Debian - Jessie.  
Итак, в файле `/etc/apt/source.list` уже есть строчка  
`deb http://mirrordirector.raspbian.org/raspbian/ jessie main contrib non-free rpi`  
нужно просто добавить ещё и эту:  
`deb http://mirrordirector.raspbian.org/raspbian/ stretch main contrib non-free rpi`  
Сохранить файл и обновить информацию о пакетах  
`$ sudo apt-get update`

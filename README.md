# Nvidia GPU Tools

*Monitoring the operation of video cards NVIDIA, control the frequency of video memory and graphics processor*

## Проверка работы видеокарт NVIDIA
***
#### *testGPU*

*Проверена работа с майнерами Ethminer, EWBF и Claymore*

Определение работы происходит по среднему значению загрузки видеокарт. Если среднее значение опускается ниже установленного предела заданного в массиве 
```shell
mavgpu=(52 52 67 76 81 84 86 88 89 90)
```
программа выполнит повторную проверку через 15 секунд, если работа карт не восстановилась будет выполнена еще одна проверка через 25 секунд. Если работа не восстановилась система будет перезагружена

Если программа обнаруживает, отсутствие интернета проверка работы видеокарт не выполняется.

Определение количества видеокарт выполняется автоматически. Если же необходимо жестко задать количество можно сделать в переменной *$setGPU* или же подключив файл настроек разгона видеокарт gpu.cfg из которого будет считано их количество.

Файл gpu.cfg должен иметь следующий вид
```shell
# Количество карт в системе
GPU_Number=2
# Настройки GPU0 
FAN_SPEED_0=80
GPU_Power_0=90
GPU_Mem_Clock_0=600
GPU_Clock_0=140
# Настройки GPU1
FAN_SPEED_1=80
GPU_Power_1=90
GPU_Mem_Clock_1=1000
GPU_Clock_1=140
``` 

Скрипт запускается в автоматическом режиме через cron с интервалом 2 минуты.
```shell
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
*/2 * * * * /usr/local/bin/testGPU
```
Скрипт можно запустить вручную используя следующие параметры
```shell
testGPU chk - ручная проверка
testGPU on  - включение проверки видеокарт
testGPU off - выключение проверки видеокарт
testGPU man - справка по работе с программой
```

При запуске без параметров, происходит включение в интерактивном режиме при котором можно выбрать, что необходимо сделать:
1. выполнить проверку
2. включить проверку видеокарт в автоматическом режиме
3. выключить проверку видеокарт в автоматическом режиме

Первые несколько минут, в автоматическом режиме, зависит от установки переменно *$delay* не проверяется работа видеокарт, это связано с тем, что некоторые майнеры не выводят загрузку видеокарт до 100% сразу после их включения, например такая работа замечена за майнером *Claymore* 

В ручном режиме проверка выполняется всегда

Программа сделана ОЧЕНЬ говорящей, в *log* файл пишется очень много информации. Это позволяет анализировать работу видеокарт и при необходимости вносить изменения в настройки майнеров.

*Алгоритм построенный на определении средней загрузки видеокарт не подходит для майнера CCMINER, так как он не стабильно загружает карты на 100%, что приводит к ложной перезагрузке системы*
***
#### *testGPU-v6*

*Проверена работа с майнерами Ethminer, EWBF, Claymore и CCMINER*

Для определение работы видеокарт используется проверка доступности карты в системе без определения ее средней загрузки.

Если мощность потребляемая картой больше 0, значит карта доступна и работает. Если карта не доступна в системе при считывании через API драйвера NVIDIA, значение мощности заменяется на 0. Если мощность хотя бы одной карты равна 0, программа повторно проверяет мощность карт и если карта все так же не доступна происходит перезагрузка системы.

При этом так же учитывается загрузка видеокарт. Это необходимо для определения не работающего майнера. Если загрузка 1 и более карт равна 0 значит майнер не работает и система будет перезагружена.
***
#### *testGPU-GUIzenity*

*Проверена работа с майнерами Ethminer, EWBF и Claymore*

Работа аналогична программе *testGPU*, с той лишь разницей, что добавлен графический интерфейс реализованный на утилите [Zenity](https://wiki.gnome.org/Projects/Zenity)

Для работы программы необходимо установить утилиту `zenity`
```shell
sudo apt install zenity
```
Кроме управления программой через графический интерфейс, возможно управление, с терминала
```shell
testGPU-GUIzenity man  - справка по работе с программой
testGPU-GUIzenity t    - ручная проверка
testGPU-GUIzenity on   - включение проверки
testGPU-GUIzenity off  - отключение проверки
testGPU-GUIzenity cron - используется для проверки работы через cron
```
***
#### *testGPU-GUIzenity-v6*
*Проверена работа с майнерами Ethminer, EWBF, Claymore и CCMINER*

Работа аналогична программе *testGPU-v6*, с той лишь разницей, что добавлен графический интерфейс реализованный на утилите [Zenity](https://wiki.gnome.org/Projects/Zenity)

Управление программой аналгично *testGPU-GUIzenity*
***
#### *infoGPU*
*Сбор и вывод в терминал информации о работе видеокарт*

Программа автоматически определяет количество видеокарт в системе, после чего используя API драйвера NVIDIA выводит основную информацию по каждой видеокарте

При желании можно вывести эту информацию на Conky, для этого необходимо в файл .conkyrc добавить
```lua
conky.text = [[
...
${color white}$hr
${color teal}${execi 5 infoGPU}$color
${color white}$hr
...
]]
```
в этом случае вывод информации о работе карт будет обновляться один раз в 5 секунд
***
#### *overclockingGPU и overclockingGPU-zenity*
*Программы для разгона видеокарт*

Для работы программы необходимо установить проприетарный драйвер для видеокарт NVIDIA и в файле `xorg.cong` включить режим разгона видеокарт ``Option "Coolbits" "31"``. Есть примеры файлов `xorg.conf` на 6, 8 и 10 карт.

Обязятельным условием является наличие файла `edid-acer-13-7.bin` - эмулятора монитора. Можно использовать любой другой `bin` файл эмулирующий работу монитора.

Настройка разгона видеокарт задается в файле `gpu.cfg`. Программа автоматически определяет количество видеокарт в системе

*overclockingGPU* - работает только с командной строки

Управление программой:
```shell
overclockingGPU pw      - ограничение мощности карт
overclockingGPU fan     - ограничение вращения вентиляторов охлаждения
overclockingGPU clock   - применение разгона карт по частоте процессора и памяти
overclockingGPU all     - применение всех параметров
```
*overclockingGPU-zenity* - аналогична программе *overclockingGPU*, с той лишь разницей, что добавлен графический интерфейс реализованный на утилите [Zenity](https://wiki.gnome.org/Projects/Zenity)

Для работы программы необходимо установить утилиту `zenity`
```shell
sudo apt install zenity
```

Управление возможно как с помощью графического интерфейса так и с командной строки

Управление программой:
```shell
overclockingGPU-zenity         - включение графического интерфейса
overclockingGPU-zenity pw      - ограничение мощности карт
overclockingGPU-zenity fan     - ограничение вращения вентиляторов охлаждения
overclockingGPU-zenity clock   - применение разгона карт по частоте процессора и памяти
overclockingGPU-zenity all     - применение всех параметров
```
***



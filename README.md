# Nvidia GPU Tools
***
*Monitoring the operation of video cards NVIDIA, control the frequency of video memory and graphics processor*

## Проверка работы видеокарт NVIDIA
***
*Файл testGPU*

*Работает с майнерами Ethminer, EWBF и Claymore*

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
testGPU chk - проверка видеокарт
testGPU on - включение проверки видеокарт
testGPU off - выключение проверки видеокарт
testGPU man - показать эту справку
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
*Файл testGPU-v6*

Если используется *CCMINER* для определение работы видеокарт используется алгоритм  проверки доступности карты в системе без определения средней загрузки видеокарт.

Если мощность потребляемая картой больше 0, значит карта доступна и работает. Если карта не доступна в системе при считывании через API драйвера NVIDIA, значение мощности заменяется на 0. Если мощность хотя бы одной карты равна 0, программа повторно проверяет мощность карт и если карта все так же не доступна происходит перезагрузка системы.

При этом так же учитывается загрузка видеокарт. Это необходимо для определения выключенного майнера. Если загрузка 1 и более карт равна 0 значит майнер не работает и система будет перезагружена.
***

# Nvidia GPU Tools
***
*Monitoring the operation of video cards NVIDIA, control the frequency of video memory and graphics processor*

## Проверка работы видеокарт NVIDIA

Скрипт запускается в автоматическом режиме через cron с интервалом 2 минуты.
```shell
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
*/2 * * * * /usr/local/bin/testGPU
```
Скрипт так же можно запустить вручную используя следующие параметры
```shell
testGPU chk - проверка видеокарт
testGPU on - включение проверки видеокарт
testGPU off - выключение проверки видеокарт
testGPU man - показать эту справку
```

Первые несколько минут, завист от установки переменно *$delay* программа в автоматическом режиме не проверяет работу видеокарт, это связяно с тем, что некторые майнеры не выводят загрузку видеокарт до 100% вразу после их включения, например такая работа замечена за майнером *Claymore* 

При запуске программы без параметров, проиходит включение в интерактивном режиме при котором можно выбрать, что необходимо сделать:
1. выполнить проверку
2. включить проверку видеокарт в автоматическом режиме
3. выключить проверку видеокарт в автоматическом режиме

В ручном режиме проверка выполняется всегда

Если обнаружено, что какая-то видеокарта не работает, программа выполнит повторную проверку через 15 секунд, будет выполнена проверка еще одна проверка через 25 секунд. Если работа не восстановилась система будет перезагружена

Если программа обнаруживает, отсутствие интернета проверка работы видеокарт не выполняется.
Программа сделана ОЧЕНЬ говорящей, в *log* файл пишется очень много инфромации. Это позволяет анализировать работу видеокарт и при необходимости вносить изменения в настройки майнеров.

Не полностью проверенная информация. Замечено, что майнер *CCMINER* не на 100% загружает видеокарты, поэтому возможно что при работе этого майнера необходимо полностью выключить проверку видеокарт или же редактировать в функции *minAvgGPU* значения переменной *$mAvGPU*


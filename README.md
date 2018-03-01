# nvidia-gpu-tools
# Monitoring the operation of video cards NVIDIA, control the frequency of video memory and graphics processor
# testGPU-v4
# Программа проверки работы видеокарт NVIDIA -V4
# Программа запускается в автоматическом режиме через cron с интервалом 1 минута. 
# Ее так же можно запустить вручную используя следующие параметры
# testGPU chk - проверка видеокарт
# testGPU on - включение проверки видеокарт с сохранением параметра во временном файле конфигурации
# testGPU off - выключение проверки видеокарт с сохраниение параметра во временном файле конфигурации
# testGPU man - показать эту справку
# Первые несколько минут, завист от установки переменно $delay программа в автоматическом 
# режиме не проверяет работу видеокарт, это связяно с тем, что некторые майнеры не 
# выводят загрузку видеокарт до 100% вразу после их включения, например такая работа 
# замечена за майнером Claymore. 
# При запуске программы без параметров, проиходит включение в интерактивном режиме
# при котором можно выбрать, что необходимо сделать, выполнить проверку, 
# включить или выключить проверку видеокарт в автоматическом режиме
# В ручном режиме проверка выполняется всегда!
# Если обнаружено, что какая-то видеокарта не работает, программа выполнит
# повторную проверку через 10 секунд, если работа карты не восстановилась
# окончательная проверка будет выполнена через следующие 20 секунд
# Если в течении этих последних 20 секунд работа карты не восстановилась
# система будет перезагружена
# Если программа обнаруживает, отсутствие интернета проверка работы видеокарт
# не выполняется
# Программа сделана ОЧЕНЬ говорящей, в log файл пишется очень много
# инфромации. Это позволяет анализировать работу видеокарт и при необходимости
# вносить изменения в настройки майнеров
# Не полностью проверенная информация. Замечено, что майнер CCMINER не на 100%
# загружает видеокарты, поэтому возможно что при работе этого майнера необходимо
# полностью выключить проверку видеокарт или же редактировать в функции minAvgGPU
# значения переменных $mAvGPU

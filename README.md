# make_C4_6-
Настройте на Server1 с помощью RSyslog отправку логов любого приложения (nginx/Jenkins/что-то еще на ваш выбор) в Elasticsearch на Server2.

Для реализации задачи выбрал приложение WEB сервер Apache.

В облаке развернул два сервера:
https://github.com/vinkin56/make_C4_6-/blob/main/servers.jpg

Несмотря на то что в Elasticsearch есть модуль чтения логов Apache, реализовал доставку логов через Rsyslog.
Настроил логи Server1 в приложении Apache:
https://github.com/vinkin56/make_C4_6-/blob/main/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20apache2%20%D0%B4%D0%BB%D1%8F%20RSysLog.jpg

Настроил на Server1 приём логов Rsyslog из Apache (conf файл в /etc/rsyslog.d/ и отправку их на Server2:
https://github.com/vinkin56/make_C4_6-/blob/main/%D0%9E%D1%82%D0%BF%D1%80%D0%B0%D0%B2%D0%BA%D0%B0%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%20rsyslog.jpg

Для первого тестирования настроил приём данных на RSyslog Server2:
https://github.com/vinkin56/make_C4_6-/blob/main/%D0%A1%D0%B5%D1%80%D0%B2%D0%B5%D1%80%20%D0%BF%D1%80%D0%B8%D1%91%D0%BC%D0%BD%D0%B8%D0%BA.jpg

Данные падают на RSyslog Server2:
https://github.com/vinkin56/make_C4_6-/blob/main/%D0%9F%D0%BE%D0%BB%D1%83%D1%87%D0%B5%D0%BD%D0%B8%D0%B5%20%D0%BE%D1%82%20RSysLog.jpg

Следующим шагом была настройка RSyslog для работы с Elasticsearch. Стандартный RSyslog не умеет работать с Elasticsearch для этого установил расширенный пакет на server1:
sudo apt install rsyslog-elasticsearch

Далее необходимо настроить на сервере отправителе server1 rsysylog на передачу в elasticsearch, а точнее в logstash. Для этого в /etc/rsyslog.d/ создал два конфигурационного файла:
1. Формирование template (шаблона) передачи данных, подключение модуля omelasticsearch и таргет отправки данных:
https://github.com/vinkin56/make_C4_6-/blob/main/0-rsyslog-elasticsearch.conf
2. Настройка отправки данных в logstash (в моём случае отправляю данные на порт 10514):
https://github.com/vinkin56/make_C4_6-/blob/main/10-apache2.conf

На сервере приемнике server2 настраиваю logstash на приём данных порт 10514:
https://github.com/vinkin56/make_C4_6-/blob/main/Logstash_conf.jpg

Кроме этого я перенастроил elasticsearch на приём log данных от агентов в локальной сети:
https://github.com/vinkin56/make_C4_6-/blob/main/elasticsearch_yml.jpg

Проверил получение данных и обратил внимание на сообщение о настройке шаблона:
https://github.com/vinkin56/make_C4_6-/blob/main/logstash_service_messege.jpg

Возможно и другие варианты настройки были рабочими, так как я не понял как добавить индекс в kibana. Но разобрался и нашёл где это делаетс:
https://github.com/vinkin56/make_C4_6-/blob/main/Add_index_to_discover.jpg

Добавил индекс logstash-

Логи поступают:
https://github.com/vinkin56/make_C4_6-/blob/main/RSysLog_to_elasticsearch.jpg

Результат получения логов событий по доступу:
https://github.com/vinkin56/make_C4_6-/blob/main/result.jpg

Так же протестировал на получение логов по ошибкам:
https://github.com/vinkin56/make_C4_6-/blob/main/apache2_error.jpg

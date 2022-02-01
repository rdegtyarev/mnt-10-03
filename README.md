# Домашнее задание к занятию "10.03. Grafana"

## Задание повышенной сложности

**В части задания 1** не используйте директорию [help](./help) для сборки проекта, самостоятельно разверните grafana, где в 
роли источника данных будет выступать prometheus, а сборщиком данных node-exporter:
- grafana
- prometheus-server
- prometheus node-exporter

За дополнительными материалами, вы можете обратиться в официальную документацию grafana и prometheus.

В решении к домашнему заданию приведите также все конфигурации/скрипты/манифесты, которые вы 
использовали в процессе решения задания.

**В части задания 3** вы должны самостоятельно завести удобный для вас канал нотификации, например Telegram или Email
и отправить туда тестовые события.

В решении приведите скриншоты тестовых событий из каналов нотификаций.

## Обязательные задания

## Задание 1
Используя директорию [help](./help) внутри данного домашнего задания - запустите связку prometheus-grafana.

Зайдите в веб-интерфейс графана, используя авторизационные данные, указанные в манифесте docker-compose.

Подключите поднятый вами prometheus как источник данных.

Решение домашнего задания - скриншот веб-интерфейса grafana со списком подключенных Datasource.

### Решение
- Используя Ansible связку Grafana-Prometeus-node_exporter на виртуальных машинах
    - Плейбук https://github.com/rdegtyarev/mnt-10-03/blob/main/infrastructure/site.yml
    - Роли:
        - Grafana https://github.com/rdegtyarev/grafana-role
        - Prometheus https://github.com/rdegtyarev/prometheus-role
        - node_exporter https://github.com/rdegtyarev/node-exporter-role
- Prometheus + Grafana + node_exporter (для мониторинга самого себя) установлены на одном хосте, на два дополнительных хоста установлены только node_exporter.
- Конфиг Prometheus: ./infrasturcture/templates/prometheus.yml.j2, job собирают метрики с node_exporter c трех хостов (включая себя).
- Скрин:
![01](https://github.com/rdegtyarev/mnt-10-03/blob/main/img/01.png)
- Для удобства утановил дашборд Node Exporter Full https://grafana.com/grafana/dashboards/1860
![02](https://github.com/rdegtyarev/mnt-10-03/blob/main/img/02.png)

---

## Задание 2
Изучите самостоятельно ресурсы:
- [promql-for-humans](https://timber.io/blog/promql-for-humans/#cpu-usage-by-instance)
- [understanding prometheus cpu metrics](https://www.robustperception.io/understanding-machine-cpu-usage)

Создайте Dashboard и в ней создайте следующие Panels:
- Утилизация CPU для nodeexporter (в процентах, 100-idle)
- CPULA 1/5/15
- Количество свободной оперативной памяти
- Количество места на файловой системе

Для решения данного ДЗ приведите promql запросы для выдачи этих метрик, а также скриншот получившейся Dashboard.

### Решение  

- Утилизация CPU для nodeexporter (в процентах, 100-idle)
    - 100 * (1 - avg by(instance)(irate(node_cpu_seconds_total{cpu="0",instance="$node",job="$job",mode="idle"}[5m])))
- CPULA 1/5/15
    - node_load1{instance="$node",job="$job"}
    - node_load5{instance="$node",job="$job"}
    - node_load15{instance="$node",job="$job"}
- Количество свободной оперативной памяти
    - node_memory_MemFree_bytes{instance="$node",job="$job"}
- Количество места на файловой системе
    - всего: node_filesystem_size_bytes{device="/dev/vda2",fstype="ext4",instance="$node",job="$job",mountpoint="/"}
    - свободно: node_filesystem_free_bytes{device="/dev/vda2",fstype="ext4",instance="$node",job="$job",mountpoint="/"}

Скрин:
![03](https://github.com/rdegtyarev/mnt-10-03/blob/main/img/03.png)

---

## Задание 3
Создайте для каждой Dashboard подходящее правило alert (можно обратиться к первой лекции в блоке "Мониторинг").

Для решения ДЗ - приведите скриншот вашей итоговой Dashboard.


### Решение
![04](https://github.com/rdegtyarev/mnt-10-03/blob/main/img/4.png)  

![05](https://github.com/rdegtyarev/mnt-10-03/blob/main/img/5.png)

---

## Задание 4
Сохраните ваш Dashboard.

Для этого перейдите в настройки Dashboard, выберите в боковом меню "JSON MODEL".

Далее скопируйте отображаемое json-содержимое в отдельный файл и сохраните его.

В решении задания - приведите листинг этого файла.

### Решение

JSON модель дашборда:  
https://github.com/rdegtyarev/mnt-10-03/blob/main/dashboard.json

---

1. Установить на сервере русскую локаль.
2. Настроить московский часовой пояс.
3. Ограничить внешний доступ к серверу: открыть порты 22,80,443 для своего IP адреса и для диапазона 91.232.196.0/24.
4. Установить Jenkins с помощью war-файла.
5. Создать сервис systemd для запуска jenkins, который в том числе будет стартовать при загрузке сервера.
6. Настроить мониторинг при помощи Prometheus: ресурсов сервера (Node exporter), а также параметров работы JVM Jenkins (JMX Exporter). Данные мониторинга вывести в Grafana (поднять на этом же сервере). 
7. Настроить доступ к поднятым сервисам по https с помощью nginx c использованием самоподписанного сертификата


ansible-galaxy init Prometheus

vi Prometheus/tasks/main.yml
	---

	- name: Prepare For Install Prometheus
	include_tasks: tasks/prepare.yml

	- name: Install Prometheus
	include_tasks: tasks/install_prometheus.yml

	- name: Install Alertmanager
	include_tasks: tasks/install_alertmanager.yml
* в первом файле будут описаны задачи для подготовки системы к установке Prometheus; во втором файле мы выполним установку системы мониторинга; в третьем файле описание для установки alertmanager.

vi Prometheus/tasks/prepare.yml

Prometheus/tasks/install_prometheus.yml
	Создаем пользователя prometheus.
	Создаем каталоги, в которые будут помещены файлы сервиса.
	Скачиваем и распаковываем архив прометея с официального сайта.
	Копируем бинарники.
	Копируем конфигурационные файлы.
	Создаем юнит в systemd.
	Стартуем сервис.

Prometheus/tasks/install_alertmanager.yml
	Создаем пользователя alertmanager.
	Создаем каталоги, в которые будут помещены файлы сервиса.
	Скачиваем и распаковываем архив alertmanager с официального сайта.
	Копируем бинарники.
	Копируем конфигурационные файлы.
	Создаем юнит в systemd.
	Стартуем сервис.

ansible-playbook --tags prom start_role.yml

Grafana
	admin / admin

Node Exporter
	nodeExp.yaml
	
	nano /etc/prometheus/prometheus.yml
		  - job_name: 'node_exporter'
            scrape_interval: 5s
            static_configs:
              - targets: ['192.168.100.119:9100']

	systemctl restart prometheus.service

SSH
	eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/naum

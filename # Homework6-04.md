
# Homework 6.04 — Alexandra Rimarenko.

---

## Задание 1

**Напишите ответ в свободной форме, не больше одного абзаца текста.**  

Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.

**Ответ:**  
Docker Compose инструмент, который позволяет запускать и управлять несколькими контейнерами Docker с помощью одного файла `docker-compose.yml`. В этом файле описываются все сервисы, тома и сети, нужные для работы приложения. Он улучшает мою жизнь тем, что позволяет запускать целую инфраструктуру одной командой, без ручного старта каждого контейнера. Это экономит время и снижает количество ошибок.

---

## Задание 2

**Создайте файл `docker-compose.yml` и внесите туда первичные настройки:**  

- `version`  
- `services`  
- `volumes`  
- `networks`  

Используйте подсеть `10.5.0.0/16`. Ваша подсеть должна называться: `Alexandra-A-my-netology-hw`.

**Файл `docker-compose.yml`:**

```yaml
version: "3.9"

services: {}

volumes: {}

networks:
  Alexandra-A-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```

---
## Задание 3

**Выполните Действия:**

1. Создайте конфигурацию `docker-compose` для **Prometheus** с именем контейнера `<ваша фамилия и инициалы>-netology-prometheus`.
2. Добавьте необходимые тома с данными и конфигурацией. Конфигурация находится в репозитории в директории `6-04/prometheus`.
3. Обеспечьте **внешний доступ к порту 9090** с Docker-сервера.
```yaml
prometheus:
    image: prom/prometheus:latest
    container_name: alexandra-a-netology-prometheus
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
      - "--storage.tsdb.path=/prometheus"
    ports:
      - "9090:9090"
    networks:
      - alexandra-a-my-netology-hw
    restart: always

```
---
## Задание 4

**Выполните Действия:**

1. Создайте конфигурацию `docker-compose` для **Pushgateway** с именем контейнера `<ваша фамилия и инициалы>-netology-pushgateway`.
2. Обеспечьте **внешний доступ к порту 9091** с Docker-сервера.

```yaml
 pushgateway:
    image: prom/pushgateway:latest
    container_name: alexandra-a-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - alexandra-a-my-netology-hw
    restart: always
    depends_on:
      - prometheus
```
---
## Задание 5

**Выполните Действия:**

1. Создайте конфигурацию `docker-compose` для **Grafana** с именем контейнера `<ваши фамилия и инициалы>-netology-grafana`.
2. Добавьте необходимые **тома** с данными и конфигурацией (конфигурация лежит в репозитории в директории `6-04/grafana`).
3. Добавьте **переменные окружения** для кастомных настроек:
   - Путь до файла с настройками должен быть в томе.
   - В самом файле пропишите:  
     ```
     логин=<ваши фамилия и инициалы>
     пароль=netology
     ```
4. Обеспечьте **внешний доступ к порту 3000** с Docker-сервера на порт 80.

```yaml
   grafana:
    image: grafana/grafana:latest
    container_name: alexandra-a-netology-grafana
    environment:
      - GF_SECURITY_ADMIN_USER=alexandra-a
      - GF_SECURITY_ADMIN_PASSWORD=netology
      - GF_PATHS_CONFIG=/etc/grafana/custom.ini
```
---
## Задание 7
**Выполните действия**.

Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.
Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.
В качестве решения приложите:

docker-compose.yml целиком;
скриншот команды docker ps после запуске docker-compose.yml;
скриншот графика, постоенного на основе вашей метрики.

**Ответ**
1.
```yaml
version: "3.9"

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: alexandra-a-netology-prometheus
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
      - "--storage.tsdb.path=/prometheus"
    ports:
      - "9090:9090"
    networks:
      - alexandra-a-my-netology-hw
    restart: always

  pushgateway:
    image: prom/pushgateway:latest
    container_name: alexandra-a-netology-pushgateway
    ports:
      - "9091:9091"
    networks:
      - alexandra-a-my-netology-hw
    restart: always
    depends_on:
      - prometheus

  grafana:
    image: grafana/grafana:latest
    container_name: alexandra-a-netology-grafana
    environment:
      - GF_SECURITY_ADMIN_USER=alexandra-a
      - GF_SECURITY_ADMIN_PASSWORD=netology
      - GF_PATHS_CONFIG=/etc/grafana/custom.ini
    volumes:
      - ./grafana:/etc/grafana
      - grafana_data:/var/lib/grafana
    ports:
      - "80:3000"
    networks:
      - alexandra-a-my-netology-hw
    restart: always
    depends_on:
      - prometheus
      - pushgateway

volumes:
  prometheus_data:
  grafana_data:

networks:
  alexandra-a-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

```
2. [Docker ps]<img width="1597" height="131" alt="Снимок экрана 2025-10-17 172908" src="https://github.com/user-attachments/assets/b9cec345-0462-4cc5-9834-daba588b375a" />
3. [Grafana]<img width="1106" height="803" alt="Снимок экрана 2025-10-17 153958" src="https://github.com/user-attachments/assets/ac6a5939-a97c-4e05-88e5-00f3828d215d" />
---
## Задание 8
**Выполните действия**:

*Остановите и удалите все контейнеры одной командой.
В качестве решения приложите скриншот консоли с проделанными действиями.*

**Ответ**
[Docker Deleted]<img width="1242" height="240" alt="Снимок экрана 2025-10-17 172853" src="https://github.com/user-attachments/assets/6c4ca951-d108-4cc0-983d-59fb8a1bf044" />





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
## Задание 7

**Выполните Действия:**
1. Поместите метрику в Pushgateway

# DevOps Tasks - Примеры запросов пользователей

Практические примеры работы с субагентом **devops-tasks** для выполнения произвольных DevOps задач.

## 📍 Контекст работы

```bash
cd ~/projects/<project>/tasks/
claude
# Субагент devops-tasks активируется автоматически
```

## ✨ Особенности контекста tasks

**Свободная работа без git ограничений!**
- ❌ НЕ требуется git status проверка
- ❌ НЕ требуется git pull
- ✅ Свободное создание и изменение файлов
- ✅ Экспериментирование без ограничений

---

## 📜 Создание скриптов

### Пример 1: Backup скрипт для PostgreSQL

**Запрос:**
```
Выполни новое задание: Создай скрипт для автоматического бэкапа базы данных PostgreSQL в S3
```

**Что делает агент:**
1. ✅ Создает директорию:
   ```bash
   mkdir -p /home/tyler/projects/<project>/tasks/postgres-backup-s3
   cd /home/tyler/projects/<project>/tasks/postgres-backup-s3
   ```

2. ✅ Создает `backup.sh`:
   ```bash
   #!/bin/bash
   set -euo pipefail

   # Переменные
   DB_HOST="${DB_HOST:-localhost}"
   DB_PORT="${DB_PORT:-5432}"
   DB_NAME="${DB_NAME}"
   DB_USER="${DB_USER}"
   S3_BUCKET="${S3_BUCKET}"
   BACKUP_DIR="/tmp/backups"

   # Создание backup
   TIMESTAMP=$(date +%Y%m%d_%H%M%S)
   BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_${TIMESTAMP}.sql.gz"

   pg_dump -h $DB_HOST -p $DB_PORT -U $DB_USER $DB_NAME | gzip > $BACKUP_FILE

   # Загрузка в S3
   aws s3 cp $BACKUP_FILE s3://$S3_BUCKET/backups/

   # Очистка старых backup-ов (> 7 дней)
   find $BACKUP_DIR -name "*.sql.gz" -mtime +7 -delete
   ```

3. ✅ Создает `config.example.env`:
   ```bash
   DB_HOST=postgres.example.com
   DB_PORT=5432
   DB_NAME=production_db
   DB_USER=backup_user
   S3_BUCKET=my-backups
   ```

4. ✅ Создает `README.md`:
   ```markdown
   # PostgreSQL to S3 Backup Script

   ## Описание
   Скрипт для автоматического резервного копирования PostgreSQL в S3

   ## Требования
   - PostgreSQL client (pg_dump)
   - AWS CLI
   - Настроенные AWS credentials

   ## Установка
   1. Скопируйте config.example.env в config.env
   2. Заполните переменные
   3. Сделайте скрипт исполняемым: chmod +x backup.sh

   ## Использование
   source config.env && ./backup.sh

   ## Автоматизация
   Добавьте в crontab:
   0 2 * * * cd /path/to/script && source config.env && ./backup.sh
   ```

5. ✅ Создает `.gitignore`:
   ```
   config.env
   *.sql
   *.sql.gz
   ```

6. ✅ Делает исполняемым: `chmod +x backup.sh`

7. ✅ Тестирует синтаксис

**Результат:**
```
Создан скрипт backup в:
/home/tyler/projects/<project>/tasks/postgres-backup-s3/

Файлы:
- backup.sh (основной скрипт)
- config.example.env (пример конфигурации)
- README.md (документация)
- .gitignore

Использование:
1. cp config.example.env config.env
2. Заполните config.env
3. ./backup.sh
```

### Пример 2: Мониторинг скрипт

**Запрос:**
```
Выполни новое задание: Создай скрипт для мониторинга подов в Kubernetes и отправки алертов в Slack
```

**Что создает агент:**
```
tasks/pod-monitor-slack/
├── monitor.sh          # Основной скрипт
├── config.yaml         # Конфигурация
├── alerts.sh           # Модуль алертов
├── README.md           # Документация
└── .env.example        # Пример переменных
```

### Пример 3: Cleanup скрипт

**Запрос:**
```
Выполни новое задание: Создай скрипт для очистки старых Docker images и volumes
```

---

## ⚙️ Генерация конфигураций

### Пример 1: Kubernetes манифесты

**Запрос:**
```
Выполни новое задание: Создай набор базовых Kubernetes манифестов для нового микросервиса
```

**Что создает агент:**
```
tasks/k8s-microservice-manifests/
├── deployment.yaml
├── service.yaml
├── ingress.yaml
├── configmap.yaml
├── secret.yaml.example
├── hpa.yaml
├── servicemonitor.yaml
├── kustomization.yaml
└── README.md
```

**deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice
  labels:
    app: microservice
spec:
  replicas: 2
  selector:
    matchLabels:
      app: microservice
  template:
    metadata:
      labels:
        app: microservice
    spec:
      containers:
      - name: app
        image: registry/microservice:latest
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
```

### Пример 2: Terraform конфигурация

**Запрос:**
```
Выполни новое задание: Сгенерируй Terraform конфигурацию для AWS инфраструктуры (VPC, EKS, RDS)
```

**Что создает:**
```
tasks/terraform-aws-infrastructure/
├── main.tf
├── variables.tf
├── outputs.tf
├── vpc.tf
├── eks.tf
├── rds.tf
├── terraform.tfvars.example
└── README.md
```

### Пример 3: Docker Compose

**Запрос:**
```
Выполни новое задание: Создай docker-compose.yml для local development (API, PostgreSQL, Redis, nginx)
```

---

## 📊 Создание мониторинга

### Пример 1: Prometheus alerts

**Запрос:**
```
Выполни новое задание: Создай Prometheus alerts для мониторинга API с алертами на высокую latency и error rate
```

**Что создает:**
```
tasks/api-prometheus-alerts/
├── alerts.yaml
├── recording-rules.yaml
├── grafana-dashboard.json
└── README.md
```

**alerts.yaml:**
```yaml
groups:
  - name: api_alerts
    interval: 30s
    rules:
      - alert: HighAPILatency
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 0.5
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High API latency detected"
          description: "95th percentile latency is {{ $value }}s"

      - alert: HighAPIErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High API error rate detected"
          description: "Error rate is {{ $value | humanizePercentage }}"

      - alert: APIDown
        expr: up{job="api-service"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "API service is down"
```

### Пример 2: Grafana dashboard

**Запрос:**
```
Выполни новое задание: Создай Grafana dashboard для мониторинга Kubernetes кластера
```

### Пример 3: Custom exporter

**Запрос:**
```
Выполни новое задание: Создай Prometheus exporter для мониторинга custom метрик приложения
```

---

## 🔧 Утилиты и инструменты

### Пример 1: CLI утилита для работы с Vault

**Запрос:**
```
Выполни новое задание: Создай CLI утилиту для упрощенной работы с Vault secrets
```

**Что создает:**
```bash
#!/usr/bin/env python3
"""
Vault CLI Helper - упрощенная работа с Vault secrets
"""

import argparse
import subprocess
import json

def vault_get(path):
    """Получить секрет из Vault"""
    result = subprocess.run(
        ['vault', 'kv', 'get', '-mount=secrets', '-format=json', path],
        capture_output=True,
        text=True
    )
    if result.returncode == 0:
        data = json.loads(result.stdout)
        return data['data']['data']
    return None

def vault_put(path, data):
    """Записать секрет в Vault"""
    cmd = ['vault', 'kv', 'put', '-mount=secrets', path]
    for key, value in data.items():
        cmd.extend([f'{key}={value}'])
    result = subprocess.run(cmd)
    return result.returncode == 0

# ... остальной код
```

### Пример 2: Log analyzer

**Запрос:**
```
Выполни новое задание: Создай утилиту для анализа логов Nginx и генерации статистики
```

### Пример 3: Certificate checker

**Запрос:**
```
Выполни новое задание: Создай скрипт для проверки всех TLS сертификатов в кластере и алертов на истечение
```

---

## 📖 Документация

### Пример 1: Runbook

**Запрос:**
```
Выполни новое задание: Создай runbook для процедуры disaster recovery
```

**Что создает:**
```markdown
# Disaster Recovery Runbook

## Сценарий: Полная потеря кластера

### Prerequisite
- Доступ к backup storage (S3)
- Terraform state сохранен
- Vault backup доступен

### Шаг 1: Восстановление инфраструктуры
1. Восстановить VPC и networking
   ```bash
   cd terraform/
   terraform init
   terraform apply -target=module.vpc
   ```

2. Восстановить Kubernetes кластер
   ```bash
   terraform apply -target=module.eks
   ```

### Шаг 2: Восстановление базовых сервисов
...
```

### Пример 2: Troubleshooting guide

**Запрос:**
```
Выполни новое задание: Создай troubleshooting guide для проблем с Kubernetes networking
```

### Пример 3: Onboarding документация

**Запрос:**
```
Выполни новое задание: Создай onboarding документацию для новых DevOps инженеров
```

---

## 🧪 Прототипирование

### Пример 1: PoC для новой технологии

**Запрос:**
```
Выполни новое задание: Создай PoC для использования Istio service mesh в нашем кластере
```

**Что создает:**
```
tasks/istio-poc/
├── install.sh          # Установка Istio
├── examples/           # Примеры конфигураций
│   ├── virtual-service.yaml
│   ├── destination-rule.yaml
│   └── gateway.yaml
├── test.sh            # Тесты функционала
├── cleanup.sh         # Очистка
├── findings.md        # Результаты тестирования
└── README.md
```

### Пример 2: Performance testing

**Запрос:**
```
Выполни новое задание: Создай набор скриптов для load testing API с использованием k6
```

### Пример 3: Эксперимент с новым подходом

**Запрос:**
```
Выполни новое задание: Создай прототип для GitOps подхода с использованием ArgoCD
```

---

## 🚀 CI/CD

### Пример 1: GitLab CI template

**Запрос:**
```
Выполни новое задание: Создай универсальный GitLab CI template для деплоя микросервисов
```

**Что создает:**
```yaml
# .gitlab-ci.yml template

stages:
  - build
  - test
  - deploy

variables:
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: "/certs"

.build-template:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  only:
    - branches

.test-template:
  stage: test
  script:
    - pytest tests/
  only:
    - branches

.deploy-template:
  stage: deploy
  image: alpine/helm:latest
  script:
    - helm upgrade --install $APP_NAME ./helm-chart
      --set image.tag=$CI_COMMIT_SHA
      --namespace $NAMESPACE
  only:
    - main
```

### Пример 2: GitHub Actions workflow

**Запрос:**
```
Выполни новое задание: Создай GitHub Actions workflow для автоматического деплоя в Kubernetes
```

### Пример 3: Jenkins pipeline

**Запрос:**
```
Выполни новое задание: Создай Jenkins pipeline для multi-stage deployment процесса
```

---

## 🔐 Security

### Пример 1: Security scanner

**Запрос:**
```
Выполни новое задание: Создай скрипт для security scan Docker images перед деплоем
```

**Что создает:**
```bash
#!/bin/bash
# Docker image security scanner

IMAGE=$1

echo "Scanning image: $IMAGE"

# Scan с Trivy
trivy image --severity HIGH,CRITICAL $IMAGE

# Scan с Grype
grype $IMAGE

# Check best practices с Dockle
dockle $IMAGE

echo "Scan complete!"
```

### Пример 2: Secrets scanner

**Запрос:**
```
Выполни новое задание: Создай скрипт для поиска hardcoded secrets в кодовой базе
```

### Пример 3: Compliance checker

**Запрос:**
```
Выполни новое задание: Создай утилиту для проверки Kubernetes resources на compliance с security policies
```

---

## 📈 Автоматизация

### Пример 1: Auto-scaling optimizer

**Запрос:**
```
Выполни новое задание: Создай скрипт для анализа использования ресурсов и рекомендаций по HPA настройкам
```

### Пример 2: Cost optimizer

**Запрос:**
```
Выполни новое задание: Создай утилиту для анализа затрат на облачные ресурсы и рекомендаций по оптимизации
```

### Пример 3: Resource cleaner

**Запрос:**
```
Выполни новое задание: Создай скрипт для автоматической очистки неиспользуемых ресурсов в Kubernetes
```

---

## 🎯 Комплексные проекты

### Пример 1: Полная система мониторинга

**Запрос:**
```
Выполни новое задание: Создай полную систему мониторинга включая:
- Prometheus setup
- Grafana dashboards
- Alert rules
- Notification channels
- Log aggregation
```

**Что создает:**
```
tasks/monitoring-system/
├── prometheus/
│   ├── prometheus.yaml
│   ├── alerts/
│   └── rules/
├── grafana/
│   ├── dashboards/
│   └── datasources/
├── alertmanager/
│   └── config.yaml
├── loki/
│   └── config.yaml
├── deploy.sh
└── README.md
```

### Пример 2: Development environment

**Запрос:**
```
Выполни новое задание: Создай полное окружение для local development с использованием Docker Compose
```

### Пример 3: Testing framework

**Запрос:**
```
Выполни новое задание: Создай testing framework для e2e тестирования микросервисной архитектуры
```

---

## 💡 Полезные советы

### Свобода творчества
В tasks/ можно экспериментировать без ограничений - нет git проверок!

### Структурированность
Каждая задача в отдельном каталоге с понятным именем

### Документация обязательна
Всегда создавайте README.md с инструкциями

### Тестируйте
Проверяйте скрипты перед использованием в production

### Переиспользование
Создавайте универсальные решения, которые можно переиспользовать

---

## 🎓 Обучающие примеры

### Для начинающих

**Запрос:**
```
Создай простой скрипт для проверки статуса Kubernetes подов с подробными комментариями
```

**Запрос:**
```
Создай tutorial по созданию Docker images с best practices
```

### Для продвинутых

**Запрос:**
```
Создай advanced скрипт для multi-cluster Kubernetes management
```

**Запрос:**
```
Создай систему для automated canary deployments с rollback
```

---

## 📝 Шаблоны README

Агент автоматически создает структурированные README:

```markdown
# Название задачи

## Описание
Краткое описание задачи и её назначения.

## Компоненты
- script.sh - основной скрипт
- config.yaml - конфигурация
- ...

## Требования
- Dependency 1
- Dependency 2

## Установка
1. Шаг 1
2. Шаг 2

## Использование
```bash
./script.sh --option value
```

## Конфигурация
Описание параметров

## Тестирование
Как протестировать

## Troubleshooting
Частые проблемы

## TODO
Что можно улучшить
```

---

**Версия:** 1.0
**Дата:** 2025-11-21

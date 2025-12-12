# DevOps Субагенты Claude

Эта директория содержит специализированных субагентов для работы с DevOps инфраструктурой на базе Kubernetes и Helmfile.

## Доступные субагенты

### 1. devops-apps
**Файл:** `devops-apps.md`
**Назначение:** Управление деплоем приложений в Kubernetes
**Рабочая директория:** `/home/tyler/projects/<project>/apps/<app-project>/<app-service>/`
**Основные задачи:**
- Масштабирование приложений
- Настройка ресурсов (CPU/Memory)
- Настройка Ingress и TLS
- Работа с ExternalSecrets
- Дебаг приложений
- CI/CD интеграция (GitLab CI)

**Когда использовать:**
- Нужно изменить количество реплик
- Добавить переменные окружения
- Настроить Ingress для приложения
- Дебаг проблем с подами
- Работа с секретами через Vault

---

### 2. devops-cluster-charts
**Файл:** `devops-cluster-charts.md`
**Назначение:** Управление инфраструктурными чартами уровня кластера
**Рабочая директория:** `/home/tyler/projects/<project>/cluster-charts/<chart-name>/stages/<cluster-name>/`
**Основные задачи:**
- Обновление версий инфраструктурных чартов (cert-manager, ingress-nginx)
- Настройка параметров cluster-level компонентов
- Дебаг инфраструктурных проблем

**Когда использовать:**
- Обновить cert-manager до новой версии
- Изменить параметры ingress-nginx
- Проверить статус cluster-level компонентов
- Дебаг проблем с инфраструктурой кластера

---

### 3. devops-shared-data
**Файл:** `devops-shared-data.md`
**Назначение:** Управление shared ресурсами между приложениями
**Рабочая директория:** `/home/tyler/projects/<project>/shared-data/stages/<namespace>/`
**Основные задачи:**
- Создание и изменение ConfigMaps
- Настройка ExternalSecrets для shared секретов
- Управление PersistentVolumeClaims
- Проверка зависимостей перед изменениями

**Когда использовать:**
- Добавить/изменить ConfigMap
- Создать shared ExternalSecret
- Проверить какие приложения используют ресурс
- Дебаг проблем с ExternalSecrets

**⚠️ ВАЖНО:** Изменения в shared-data влияют на множество приложений! Субагент всегда проверяет зависимости перед изменениями.

---

### 4. devops-tasks
**Файл:** `devops-tasks.md`
**Назначение:** Выполнение произвольных DevOps задач, создание скриптов и утилит
**Рабочая директория:** `/home/tyler/projects/<project>/tasks/<task-name>/`
**Основные задачи:**
- Создание скриптов автоматизации
- Генерация конфигурационных файлов
- Прототипирование решений
- Создание документации
- Разработка утилит

**Когда использовать:**
- Создать backup скрипт
- Сгенерировать Kubernetes манифесты
- Разработать утилиту мониторинга
- Создать документацию
- Прототипировать решение

**Особенность:** В tasks/ НЕ требуется git синхронизация - можно свободно создавать и экспериментировать!

---

### 5. jira-tasks
**Файл:** `jira-tasks.md`
**Назначение:** Управление задачами в Jira через CLI инструмент jira-cli
**Рабочая директория:** Любая (универсальный агент)
**Основные задачи:**
- Просмотр и фильтрация задач
- Создание новых задач (Bug, Task, Story)
- Изменение статуса задач
- Логирование времени работы
- Добавление комментариев и описаний
- Работа с метками, приоритетами, спринтами

**Когда использовать:**
- Посмотреть мои задачи в работе
- Создать новый баг или задачу
- Перевести задачу в другой статус
- Залогировать время работы
- Добавить комментарий к задаче
- Получить отчет по задачам

**Особенность:** Работает через [jira-cli](https://github.com/ankitpokhrel/jira-cli), read-only операции без подтверждения!

---

### 6. devops-terraform
**Файл:** `devops-terraform.md`
**Назначение:** Управление облачной инфраструктурой с использованием Terraform
**Рабочая директория:** `/home/tyler/projects/<project>/terraform/stages/<project>/<stage>/`
**Основные задачи:**
- Управление AWS инфраструктурой (EKS, VPC, S3, IAM, Route53)
- Управление GCloud ресурсами (Compute Engine, GKE, Cloud Storage)
- Управление DigitalOcean (Droplets, Kubernetes, Spaces)
- Разработка и изменение Terraform модулей
- Import существующих ресурсов при изменении структуры
- State management и troubleshooting
- Работа с множеством провайдеров

**Когда использовать:**
- Создать новую AWS EKS инфраструктуру
- Масштабировать EKS Node Groups
- Изменить структуру модулей и импортировать ресурсы
- Создать/изменить S3 buckets через модули
- Обновить Kubernetes версию в EKS
- Решить проблемы с AWS SSO/GCloud/DO аутентификацией
- Создать новый Terraform модуль
- Добавить новые cloud ресурсы

**Особенность:** Работа с несколькими cloud провайдерами, управление state, import ресурсов при рефакторинге!

**⚠️ ВАЖНО:** Изменения модулей влияют на все stages использующие этот модуль!

---

## Автоматическое определение контекста

Claude Code автоматически определяет нужного субагента по текущей рабочей директории:

```
/home/tyler/projects/<project>/apps/*/*                    → devops-apps
/home/tyler/projects/<project>/cluster-charts/*/*          → devops-cluster-charts
/home/tyler/projects/<project>/shared-data/stages/*        → devops-shared-data
/home/tyler/projects/<project>/tasks/*                     → devops-tasks
/home/tyler/projects/<project>/terraform/stages/*/*        → devops-terraform
```

## Явное использование субагента

Вы также можете явно запросить использование конкретного субагента:

```
> Use devops-apps subagent to scale my application to 5 replicas
> Use devops-shared-data subagent to check which apps use this ConfigMap
> Use devops-tasks subagent to create a backup script
> Use jira-tasks subagent to show my current tasks in progress
> Use devops-terraform subagent to create new EKS cluster in prod-indonesia
```

## Общие принципы работы субагентов

### Разрешения

**✅ Без подтверждения (read-only):**
- Чтение файлов
- kubectl get/describe/logs
- helmfile diff/template
- vault kv get/list
- Web search

**⚠️ Требует подтверждения:**
- Изменение файлов
- kubectl apply/patch/delete
- helmfile sync
- vault kv put/patch/delete

### Git синхронизация

**apps, cluster-charts, shared-data, terraform:**
- Перед началом работы проверяют git status
- При незакоммиченных изменениях - предупреждают пользователя
- Выполняют git pull для синхронизации

**tasks:**
- НЕ проверяет git status
- Свободная работа без ограничений

### Работа с Vault

Все субагенты работают с HashiCorp Vault через CLI:
- KV engine с именем "secrets"
- Чтение секретов без подтверждения
- Запись/изменение требует подтверждения

---

## Создание новых субагентов

Структура файла субагента:

```markdown
---
name: уникальное-имя
description: Описание назначения на английском (для автовыбора)
tools: Read, Write, Edit, Bash, Grep, Glob, WebFetch, WebSearch
model: inherit
---

# Название субагента

Основное содержимое на русском языке с инструкциями, примерами и чеклистами.
```

**Размещение:**
- Пользовательские субагенты: `~/.claude/agents/` (все проекты)
- Проектные субагенты: `.claude/agents/` (текущий проект)

---

## Полезные ссылки

- [Документация Claude Code по субагентам](https://code.claude.com/docs/en/sub-agents)
- [Основная MCP документация](/home/tyler/projects/ai-docs/MCP_DOCUMENTATION.md)
- [Исходная документация по контекстам](/home/tyler/projects/ai-docs/)

---

**Версия:** 1.0
**Дата:** 2025-11-21
**Автор:** DevOps Team

# Домашняя инфраструктура

Этот проект — автоматизированная настройка домашнего сервера с использованием Ansible и Docker.

Цель — получить базовые навыки работы с Ansible, попрактиковаться в работе с Docker и в результате чего создать модульную, расширяемую и безопасную инфраструктуру, под домашнее использование.

---

## Особенности

- Полный доступ только через WireGuard VPN
- Все сервисы работают в Docker-контейнерах
- Используются отдельные docker-сети (internal, proxy) для безопасного взаимодействия между сервисами
- Внешний доступ полностью закрыт (без проброса портов)
- Все внутренние веб-сервисы доступны по локальным доменам типа *.local через Caddy
- Возможность масштабирования и модульного расширения

---

## Архитектура
- ОС: AlmaLinux 
- Доступ администратора: WireGuard VPN   
- Контейнеризация: Docker  

Сети:
- proxy: для reverse-proxy и публичных веб-интерфейсов
- internal: для взаимодействия между backend-сервисами

---

<!-- ## 📦 Основные сервисы

| Сервис         | Назначение                 | Сеть         | Доступ  |
| -------------- | -------------------------- | ------------ | ------- |
| WireGuard      | VPN-доступ                 | — (хост)     | 🔒      |
| Docker Compose | Запуск и управление        | —            | 🔒      |
| Caddy          | Reverse proxy, локальные домены | proxy | 🔒      |
| Gitea          | Git-сервер                 | proxy, internal | 🔒  |
| Woodpecker CI  | CI/CD                      | proxy, internal | 🔒  |
| BookStack      | Wiki/документация          | proxy, internal | 🔒  |
| Taiga          | Канбан-доска               | proxy, internal | 🔒  |
| PostgreSQL     | База данных                | internal   | 🔒      |
| Portainer      | Управление Docker          | proxy      | 🔒      |
| Netdata        | Мониторинг                 | proxy      | 🔒      |
| Restic         | Бэкапы                     | — (хост)     | 🔒      |
| Fail2ban       | Защита от brute-force      | — (хост)     | 🔒      | -->

## Требования

- Целевая система AlmaLinux 10
- Ansible 2.18.5 (на момент начала проекта)
- Docker 28.2.2 (на момент начала проекта)
<!-- - Docker Compose v2 -->
<!-- - Git -->


---

## Быстрый старт

```bash
# 1. Склонировать репозиторий
git clone https://github.com/AlexandrShapkin/homelab-infra.git
cd homelab-infra

# 2. Обновить inventory под своё окружение
vim inventory/hosts

# 3. Создать файл vault_pass.txt в котором разместить пароль от хранилища 

# 4. Создать vault с требуемой информацией
# (см. inventory/group_vars/servers/vault.example.yml)
ansible-vault create inventory/group_vars/servers/vault.yml

# 5. Запустить плейбук
ansible-playbook site.yml --ask-become-pass # запросит пароль
# либо
ansible-playbook playbook/<назване плейбука>.yml --ask-become-pass # запросит пароль
```
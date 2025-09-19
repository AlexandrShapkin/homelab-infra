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

## Основные сервисы

| Сервис         | Назначение                      | Сеть              |
| -------------- | ------------------------------- | ----------------- |
| WireGuard      | VPN-доступ                      | — (хост)          |
| Docker Compose | Запуск и управление             | —                 |
| Traefik        | Reverse proxy, локальные домены | proxy             |
| Gitea | GitLab | Git-сервер                      | proxy, internal   |
| PostgreSQL     | База данных                     | internal, (proxy) |
<!-- | Portainer      | Управление Docker               | proxy             |
| Netdata        | Мониторинг                      | proxy             |
| Restic         | Бэкапы                          | — (хост)          |
| Fail2ban       | Защита от brute-force           | — (хост)          |
| Woodpecker CI  | CI/CD                           | proxy, internal   |
| BookStack      | Wiki/документация               | proxy, internal   |
| Taiga          | Канбан-доска                    | proxy, internal   | -->

## Требования

- Целевая система AlmaLinux 10
- Ansible 2.18.5 (на момент начала проекта)
- Docker 28.2.2 (на момент начала проекта)
- Docker Compose v2
- Git
- Python > 3.12


---

## Быстрый старт

```bash
# 1. Склонировать репозиторий
git clone https://github.com/AlexandrShapkin/homelab-infra.git
cd homelab-infra

# 2. Обновить inventory под своё окружение
vim inventory/hosts.yml

# 3. Создать файл vault_pass.txt в котором разместить пароль от хранилища 
echo "<пароль от хранилища>" > vault_pass.txt

# 4. Создать vault с требуемой информацией
# (см. inventory/group_vars/all/vault.example.yml)
ansible-vault create inventory/group_vars/all/vault.yml

# 5. Создать и заполнить хранилища сервисов <название сервиса>-vault.yml
# (см. inventory/group_vars/all/<название сервиса>-vault.example.yml)
ansible-vault create inventory/group_vars/all/<название сервиса>-vault.yml

# 6. Создать и заполнить хранилища хостов <хост>.yml
# (см. inventory/host_vars/<хост>.example.yml)
ansible-vault create inventory/host_vars/<название сервиса>.yml

# 7. Запустить плейбук
ansible-playbook site.yml
# либо
ansible-playbook playbook/<назване плейбука>.yml
# Если в host_vars не заданы хранилища паролей для хостов,
# то требуется использовать ключ --ask-become-pass.
# Но это не рекомендованный вариант, так как пароли
# для localhost и целевого хоста могут не совпадать
```
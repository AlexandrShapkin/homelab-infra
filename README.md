# homelab-infra

GitOps-репозиторий для домашнего кластера k3s, управляемого через ArgoCD.

На предыдущем этапе я использовал для домашнего сервера Docker Compose + Ansible и в качестве ОС Almalinux. Найти старые наработки можно здесь - https://github.com/AlexandrShapkin/homelab-infra/tree/legacy/docker-compose.

## Структура репозитория

```
homelab-infra/
├── apps/                        # App of Apps — ArgoCD Applications
│   ├── root-app.yaml            # Корневой app, следит за этой директорией
│   └── ...                      # App
├── clusters/
│   └── homelab/
│       ├── kustomization.yaml
│       └── root-app.yaml        # Bootstrap-манифест
└── infrastructure/
    └── prometheus-stack/        # Plain YAML манифесты
        └── ...
```

## Bootstrap (первый запуск)

Предполагается, что ArgoCD уже установлен в неймспейсе `argocd`.

```bash
# Применить корневой Application — дальше ArgoCD сам разворачивает всё остальное
kubectl apply -f clusters/homelab/root-app.yaml
```

После этого в ArgoCD появится `root-app`, который подхватит `apps/` и создаст
все остальные Applications автоматически.
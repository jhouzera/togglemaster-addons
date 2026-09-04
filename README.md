# togglemaster-addons

Repositorio dedicado aos addons de infraestrutura do cluster EKS da plataforma ToggleMaster.

Proposito:
- Centralizar a camada de plataforma e observabilidade basica do cluster.
- Permitir versionamento e governanca separados dos manifests das aplicacoes.

Responsabilidades:
- Metrics Server.
- KEDA.
- NGINX Gateway Fabric.
- External Secrets Operator.
- Stakater Reloader.

Este repositorio concentra apenas a camada de plataforma instalada localmente via Helm.
- Checklist operacional no ambiente dev: `docs/CHECKLIST-DEV.md`.
- Runbook operacional no ambiente dev: `docs/RUNBOOK-DEV.md`.

Dependencias externas:
- Depende do cluster EKS provisionado pelo `togglemaster-iac` e de um contexto `kubectl` autenticado.

Convenção de nomenclatura do laboratorio:
- Roles IRSA: `togglemaster-dev-<addon>-irsa`
- Prefixo ECR: `togglemaster-dev`
- Secrets do AWS Secrets Manager: `togglemaster-dev/app/<secret-name>`

O ArgoCD reconcilia somente os microsservicos declarados no repositorio GitOps. A promocao de
imagens e executada pelo GitHub Actions, sem credenciais Git de escrita no cluster.

## Instalacao local

Com acesso administrativo ao EKS, execute na raiz deste repositorio:

```bash
export ADDONS_ENVIRONMENT=dev
export AWS_ACCOUNT_ID=927738510198
export AWS_REGION=us-east-1

bash scripts/install-addons.sh
```

O script e idempotente, valida o contexto `kubectl`, cria os namespaces necessarios e instala
Metrics Server, KEDA, NGINX Gateway Fabric, External Secrets e Reloader com versoes fixadas.
Ele substitui o marcador `<env>` somente em arquivos temporarios, preservando os values
versionados.


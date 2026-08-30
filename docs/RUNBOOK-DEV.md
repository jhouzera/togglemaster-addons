# Runbook de Operacao do Repositorio Addons (Dev)

## Objetivo
Operar a camada de plataforma do cluster EKS (`metrics-server`, `keda`, `nginx-gateway`, `external-secrets`, `reloader`) no ambiente `dev`.

## Escopo
- Versionamento de values por addon.
- Sincronizacao declarativa via ApplicationSet no ArgoCD.
- Validacao da saude dos componentes de plataforma.

## Pré-requisitos
- ArgoCD instalado no cluster.
- ApplicationSet de addons apontando para este repositório.
- Roles/IRSA já provisionadas pelo repositório de IaC.
- Os charts externos e suas versões devem permanecer fixos no ApplicationSet do GitOps.

## Configuração manual

1. Confirme que o GitHub Environment `dev`, as variables AWS e a role OIDC foram criados
	conforme o runbook do `togglemaster-iac`.
2. Em `Settings > Branches`, proteja a branch do laboratorio e exija os checks do Pull Request.

## Estrutura esperada
- `addons/metrics-server/values.yaml`
- `addons/keda/values.yaml`
- `addons/nginx-gateway/values.yaml`
- `addons/external-secrets/values.yaml`
- `addons/reloader/values.yaml`

## Procedimento de execução

### 1. Alteração em addon
1. Criar branch e alterar o `values.yaml` do addon alvo.
2. Abrir Pull Request para `main`.
3. Revisar impacto (namespace, recursos, annotations e IRSA).
4. Fazer merge após aprovação.

### 2. Sincronizacao no ArgoCD
1. Confirmar que o Application correspondente foi reconciliado.
2. Confirmar pods saudáveis no namespace do addon.
3. Validar eventos para detectar falhas de configuração.

### 3. Validação funcional mínima
1. `metrics-server`: `kubectl top nodes` responde.
2. `keda`: `ScaledObject` em `analytics` sem erro de reconciliador.
3. `nginx-gateway`: `GatewayClass` existente e controlada.
4. `external-secrets`: `ClusterSecretStore` e `ExternalSecrets` reconciliando.
5. `reloader`: rollout automático ao atualizar segredo/configmap.

## Comandos de apoio

```bash
kubectl get pods -n kube-system
kubectl get pods -n keda
kubectl get pods -n nginx-gateway
kubectl get pods -n external-secrets
kubectl get pods -n reloader
kubectl get gatewayclass
kubectl get externalsecrets -A
```

## Troubleshooting
- Pod em CrashLoop: revisar values, limites de recursos e eventos.
- Falha de acesso AWS: revisar annotations IRSA e permissões da role.
- Addon nao sincroniza: revisar ApplicationSet de addons no GitOps.
- External Secrets nao reconcilia: confirmar que os secrets foram criados no AWS Secrets
	Manager pelo `togglemaster-secrets-generator` seguindo o padrao
	`togglemaster-dev/app/<secret-name>` (ex.: `togglemaster-dev/app/service-api-key`)
	e que a ServiceAccount/role IRSA coincidem.

## Critério de sucesso
- Todos os addons sincronizados e saudáveis no `dev`.
- Mudancas de values aplicadas via ArgoCD sem intervenção manual.
- Camada de plataforma pronta para suportar os microsservicos.

# Checklist de Integracao GitHub + Addons (Ambiente Dev)

## 1. Preparacao inicial
- [ ] Confirmar que o repositório `togglemaster-addons` esta acessivel para leitura pelo ArgoCD.
- [ ] Confirmar que os values dos addons estao organizados em `addons/*/values.yaml`.
- [ ] Confirmar que os namespaces de destino dos addons estao definidos corretamente.

## 2. Estrutura obrigatoria do repositório
- [ ] `addons/metrics-server/values.yaml` presente.
- [ ] `addons/keda/values.yaml` presente.
- [ ] `addons/nginx-gateway/values.yaml` presente.
- [ ] `addons/external-secrets/values.yaml` presente.
- [ ] `addons/reloader/values.yaml` presente.

## 3. Configuracao de Actions no GitHub
Em `Settings > Actions > General`:
- [ ] Execucao de workflows permitida (se houver validação automatica).
- [ ] Actions de terceiros permitidas pela politica da organizacao.

## 4. Protecao da branch main (recomendado)
- [ ] Exigir Pull Request para merge.
- [ ] Exigir revisão de codigo.
- [ ] Exigir checks obrigatorios antes de merge.
- [ ] Bloquear merge com checks falhando.

## 5. Integracao com togglemaster-gitops
- [ ] Confirmar que `bootstrap/applicationsets/addons.yaml` no `togglemaster-gitops` aponta para este repositório.
- [ ] Confirmar `targetRevision` esperado (ex: `main`).
- [ ] Confirmar paths `addons/*/values.yaml` coerentes com o repositório atual.

## 6. Validacao dos addons no cluster
- [ ] `metrics-server` com pods saudaveis em `kube-system`.
- [ ] `keda` com pods saudaveis em `keda`.
- [ ] `nginx-gateway` com pods saudaveis em `nginx-gateway`.
- [ ] `external-secrets` com pods saudaveis em `external-secrets`.
- [ ] `reloader` com pods saudaveis em `reloader`.

## 7. Validacao de IRSA (quando aplicavel)
- [ ] ServiceAccount do addon com annotation `eks.amazonaws.com/role-arn` correta.
- [ ] Role IAM correspondente existente no ambiente `dev`.
- [ ] Permissoes da role condizentes com o addon.

## 8. Teste funcional de sincronizacao
- [ ] Alterar um valor simples de addon em PR (ex: recurso CPU/memoria).
- [ ] Fazer merge na `main`.
- [ ] Confirmar que ArgoCD sincronizou automaticamente o addon alterado.

## 9. Troubleshooting rapido
- [ ] Se addon nao instala: revisar repo/chart/version no ApplicationSet.
- [ ] Se addon nao sobe: revisar values e eventos do namespace.
- [ ] Se falhar permissao AWS: revisar role/IRSA e trust policy.

## 10. Criterio de pronto
- [ ] Os 5 addons sincronizam automaticamente no ambiente `dev`.
- [ ] Alteracoes de values no repositório refletem no cluster via ArgoCD.
- [ ] Addons operam com configurações consistentes e sem intervenção manual.

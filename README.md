# togglemaster-addons

Repositório responsável pela camada estrutural de **Addons** do cluster Kubernetes (EKS).

## 🎯 Propósito
Separar a governança e o ciclo de vida das aplicações (Apps) dos componentes de sistema e plataforma.
Contém ferramentas fundamentais para que as aplicações funcionem com resiliência, exposição pública e acesso à dados sensíveis.

## 🛠️ Componentes Inclusos
- **NGINX Gateway Fabric**: Provedor oficial da API Gateway moderna para balanceamento e exposição L7 (`togglemaster-gateway`).
- **External Secrets Operator (ESO)**: Sincroniza segredos armazenados no AWS Secrets Manager em Secrets nativos do K8s, abstraindo credenciais dos *deployments*.
- **Metrics Server**: Coletor de métricas vital para Auto-Scaling (HPA).

## 🚀 Como Utilizar
Alterações nas versões dos Helm Charts ou ajustes nos valores dos addons devem ser comitados aqui.
O repositório é espelhado no ArgoCD por um `ApplicationSet`, que cuida da instalação e gestão ciclo a ciclo no cluster.

### Exemplo Simples
Para alterar os IP's permitidos a acessar a API Gateway (ex: range do Cloudflare):
Modifique o bloco `loadBalancerSourceRanges` no arquivo de `values.yaml` do NGINX Gateway, e o ArgoCD aplicará a regra de *Security Group* instantaneamente.

# togglemaster-addons

Repositório responsável pela camada estrutural de **Addons** do cluster Kubernetes (EKS).

## 🎯 Propósito
Separar a governança e o ciclo de vida das aplicações (Apps) dos componentes de sistema e plataforma.
Contém ferramentas fundamentais para que as aplicações funcionem com resiliência, exposição pública e acesso à dados sensíveis.

## 🛠️ Componentes Inclusos
- **NGINX Gateway Fabric**: Provedor oficial da API Gateway moderna para balanceamento e exposição L7 (`togglemaster-gateway`).
- **External Secrets Operator (ESO)**: Sincroniza segredos armazenados no AWS Secrets Manager em Secrets nativos do K8s, abstraindo credenciais dos *deployments*.
- **Metrics Server**: Coletor de métricas vital para Auto-Scaling (HPA).

## ⚙️ Como Funciona
Ao isolar ferramentas de plataforma das ferramentas de negócio, alcançamos maior estabilidade.
O ArgoCD (via `ApplicationSet`) escaneia este repositório em busca de Helm Charts de provedores. 
- Quando o **NGINX Gateway Fabric** é provisionado por aqui, ele automaticamente cria um Network Load Balancer (NLB) na AWS para expor as APIs da plataforma usando o padrão de mercado *Gateway API*.
- Quando o **External Secrets Operator** é provisionado, ele passa a observar regras criadas nos manifestos e puxa dados confidenciais do AWS Secrets Manager para a memória do Kubernetes como Secrets nativos.

## 🚀 Como Utilizar
Alterações nas versões dos Helm Charts ou ajustes nos valores dos addons devem ser comitados aqui.
O repositório é espelhado no ArgoCD por um `ApplicationSet`, que cuida da instalação e gestão ciclo a ciclo no cluster.

### Exemplo Simples
Para alterar os IP's permitidos a acessar a API Gateway (ex: range do Cloudflare):
Modifique o bloco `loadBalancerSourceRanges` no arquivo de `values.yaml` do NGINX Gateway, e o ArgoCD aplicará a regra de *Security Group* instantaneamente.

TESTE
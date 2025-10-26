# Projeto - GitOps na prática - Online Boutique

Esse projeto tem como objetivo implantar um conjunto de microserviços (**Online Boutique**, da Google Cloud Platform) em um cluster **Kubernetes local** utilizando **ArgoCD** e o conceito de **GitOps**, para implantar um aplicativo de forma automatizada. Vicenciando na prática como as empresas modernas operam em ambientes cloudnative.

---

## Pré-requisitos
• Docker Desktop instalado (com Kubernetes habilitado)  
• Kubectl configurado (kubectl get nodes funcionando)  
• ArgoCD instalado no cluster  
• Conta no GitHub com repositório público  
• Git instalado  
• Docker funcionando localmente  

---
## Etapas do Projeto

### Etapa 1 - Criar o repositório no GirHub
• Acessar o repositório <a href="https://github.com/GoogleCloudPlatform/microservices-demo" target="_blank">Google CLoud Platform</a>  
• Efeturar um fork deste repositório  
• Após, crie um repositório **público** chamado `gitops-microservises`  
• Adicionar o arquivo:  
 [`k8s/online-boutique.yaml`](https://github.com/GoogleCloudPlatform/microservices-demo/blob/main/release/kubernetes-manifests.yaml)  
• Confirmar que está acessível publicamente:  
https://github.com/<SEU_USUARIO>/gitops-microservices.git  

---

### Etapa 2 - Instalar o ArgoCD  
• No terminal WSL da sua máquina local, efetue:  
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
• Verifique se está rodando:
```bash
kubectl get pods -n argocd
```
Para prosseguir, todos devem estar com **STATUS = Running**.
<img width="674" height="132" alt="image" src="https://github.com/user-attachments/assets/876f8e17-fc42-4211-9c57-9227bfe918a7" />

---

### Etapa 3 - Acessar o ArgoCD localmente
• Para poder acessar o ArgoCD via localhost, utilize esse comando no seu terminal WSL:  
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
• Abra no navegador este link:  
https://localhost:8080
• Para encontrar a senha que foi gerada pelo ArgoCD, utilize este código no terminal:  
```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d && echo
```
• Usuário: admin  

---

### Etapa 4 - Criar o app no ArgoCD
• Acesse o *painel → Settings → Repositories → Connect Repo*    
• Connection: Via HTTPS  
• URL: `https://github.com/<SEU_USUARIO>/gitops-microservices.git`  
<sub> _(Deixe Username/Password em branco)_ </sub>  
<img width="1682" height="337" alt="Screenshot_2" src="https://github.com/user-attachments/assets/abd3991a-d44f-401e-acbc-16475df81936" />

• Vá em + NEW APP e preencha:
| Campo | Valor |
|--------|--------|
| Application Name | `online-boutique` |
| Project | `default` |
| Repository URL | `https://github.com/<SEU_USUARIO>/gitops-microservices.git` |
| Revision | `HEAD` |
| Path | `k8s` |
| Cluster URL | `https://kubernetes.default.svc` |
| Namespace | `default` |

• Clique em *Create → Sync → Synchronize*  
  • Verifique se está funcionando:
```bash
kubectl get pods
```
<sub> *(Todos os serviços devem estar **Running** )* </sub> 
<img width="1689" height="198" alt="Screenshot_6" src="https://github.com/user-attachments/assets/c6016ebf-93dc-480a-937a-681ad2e11672" />

---

### Etapa 5 - Acessar o Frontend

• Como o serviço `frontend` é do tipo ClusterIP, então faça o port-forward:  
```bash
kubectl port-forward svc/frontend 8081:80
```
• Seguinte, no seu navegador, acesse:  
http://localhost:8081

• Site da Online Boutique funcionando:  
<img width="1905" height="918" alt="Screenshot_7" src="https://github.com/user-attachments/assets/f35b61d3-afab-441f-ab29-76624ab6479b" />

---

### Autor
**Guilherme Assumpção Lourenzo**


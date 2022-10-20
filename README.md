# Projeto conversão de temperatura - Aula 01 - Jornada DevOps de Elite


## Início do Projeto

### Clonando o projeto Conversão de Temperatura

$ git clone https://github.com/emanuelfds/conversao-temperatura.git

#### Criando e Editando o Dockerfile

```
FROM node:18.11.0
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD ["node","server.js"]
```

### Executando o Build no diretório corrente observando a nomemclatura da TAG

$ docker build -t emanuelfds/conversao-temperatura:v1 .

### Listando a imagem criada

$ docker image ls | grep -i conversao-temperatura

### Executando o container com a imagem criada em modo "Deamon" com bind na porta 8080

$ docker run -d --name aula01 -p 8082:8080 emanuelfds/conversao-temperatura:v1

### Listar todos container, em execução ou não

$ docker ps -a

### Remover um container em execução

$ docker rm -f "CONTAINER ID" OR "CONTAINER NAME"

### Criando o Deployment e o Service

$ vim deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: conversor
  namespace: jornada-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: site
        image: emanuelfds/conversao-temperatura:v1
        resources:
          limits:
            memory: 64Mi
            cpu: "0.4"
          requests:
            memory: 32Mi
            cpu: "0.2"
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: web-page
  namespace: jornada-devops
spec:
  selector:
    app: web
  type:  NodePort
  ports:
    - port:  80
      protocol: TCP
      nodePort: 30000
```

### Aplicanto o manifesto

$ kubectl apply -f deployment.yaml

# Criando o Port-Forward para o service

$ kubectl port-forward svc/web-page 8080:80 --namescpace jornada-devops

### Verificarndo os pods

$ kubectl get pods --namespace jornada-devops

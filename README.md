# KubeScale: Distributed Async Processor 🚀

![.NET 10](https://img.shields.io/badge/.NET-10-512BD4?logo=dotnet)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?logo=rabbitmq&logoColor=white)
![MassTransit](https://img.shields.io/badge/MassTransit-Messaging-blue)

Este projeto demonstra uma arquitetura de microserviços de alta escalabilidade projetada para resolver problemas de **I/O Bound** e latência em sistemas distribuídos. Utilizando o padrão de mensageria assíncrona, a solução é capaz de absorver picos de carga massivos e processá-los de forma resiliente.

## 🧠 O Desafio Técnico
Muitas aplicações falham ao tentar processar requisições pesadas de forma síncrona, causando *timeouts* e esgotamento de *threads*. 
**A Solução:** Implementar um **Buffer de Mensagens** com RabbitMQ e um parque de consumidores (Workers) que escalam horizontalmente no Kubernetes via **HPA (Horizontal Pod Autoscaler)** conforme a demanda da fila aumenta.

## 🏗️ Arquitetura do Sistema



### Componentes:
- **Gateway.API:** Ponto de entrada REST (Producer) que converte comandos em eventos assíncronos.
- **RabbitMQ:** Message Broker responsável pelo desacoplamento e persistência das mensagens.
- **Worker.Consumer:** Serviço de processamento (Consumer) com lógica de Retry e concorrência otimizada.
- **Slow.API:** Mock de serviço externo com latência induzida para validar o comportamento de escalonamento.
- **Shared Kernel:** Biblioteca de contratos de dados (POCOs) compartilhada entre os serviços.

## 🛠️ Tecnologias e Padrões
- **Framework:** .NET 10 (C#)
- **Mensageria:** MassTransit (Abstração sobre RabbitMQ)
- **Containerização:** Docker & Dockerfiles multi-stage
- **Orquestração:** Kubernetes (Deployments, Services, HPA)
- **Resiliência:** Retry Policies e Dead Letter Queues (DLQ)

## ☸️ Como Executar no Kubernetes (Minikube)

### 1. Inicializar o Cluster
```
minikube start
minikube addons enable metrics-server
```
### 2. Build das Imagens (No ambiente Minikube)

# Configure o terminal para usar o Docker do Minikube
```
& minikube -p minikube docker-env | Invoke-Expression
```
# Build das imagens
```
docker build -t gateway-api:latest -f src/Gateway.API/Dockerfile .
docker build -t worker-consumer:latest -f src/Worker.Consumer/Dockerfile .
docker build -t slow-api:latest -f src/Slow.API/Dockerfile .
```
### 3. Deploy dos Manifestos
```
kubectl apply -f infra/k8s/
```
### 4. Teste de Carga e Escalonamento
Para observar o Kubernetes criando novos Pods automaticamente enquanto a fila processa:
# Acompanhe o HPA em tempo real
```
kubectl get hpa -w
```

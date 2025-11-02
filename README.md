# Executando-Tarefas-Automatizadas-com-Lambda-Functions-e-S3
Desafio: Executando Tarefas Automatizadas com Lambda Functions e S3

Padrão de Arquitetura: S3 + Lambda (Orientado a Eventos)
Neste modelo, o Amazon S3 atua como a fonte de eventos (o gatilho), e o AWS Lambda atua como o processador de eventos (a função).

Como Funciona a Integração
Gatilho (S3): Você configura um evento em um bucket S3 (ex: um novo arquivo é carregado, um arquivo é excluído).

Notificação: Quando o evento ocorre, o S3 envia uma notificação para o Lambda.

Execução (Lambda): O Lambda executa automaticamente um código (em Python, Node.js, etc.) para processar o arquivo recém-chegado, sem a necessidade de manter um servidor ligado.

## ** Arquitetura e Fluxo Automatizado**

O fluxo cria um *pipeline* onde o recebimento de dados (**S3**) aciona o processamento (**Lambda**), que armazena os resultados (**DynamoDB**), e são consultados via **API Gateway**.

| Serviço | Função na Arquitetura |
| :--- | :--- |
| **AWS S3** | **Gateway de Ingestão:** Recebe arquivos JSON e dispara o evento. |
| **AWS Lambda** | **Processador Serverless:** Executa o código sob demanda para processar dados. |
| **AWS DynamoDB** | **Banco de Dados NoSQL:** Armazena os currículos processados. |
| **API Gateway** | **Camada de Consulta:** Expõe o DynamoDB via REST API para o RH. |
| **LocalStack** | **Simulação Local:** Permite o desenvolvimento e teste de todos os serviços AWS. |

### **Fluxo de Dados:**

1.  **Ingestão:** Arquivo JSON é enviado para o **S3**.
2.  **Automação:** O *upload* dispara a **Lambda** (`S3CurriculoProcessor`).
3.  **Processamento:** A Lambda insere o conteúdo do JSON no **DynamoDB**.
4.  **Consulta:** O RH acessa o **API Gateway**, que busca e retorna os dados via uma segunda Lambda.


## **Etapas Chave da Implementação**

O desafio foi dividido em duas etapas principais, todas executadas via comandos CLI no ambiente simulado do LocalStack:

### ** Ingestão e Processamento (Pipeline Serverless)**

* **Preparação:** Inicialização do LocalStack via Docker. Criação do **Bucket S3** e da **Tabela DynamoDB**.
* **Provisionamento:** Criação e empacotamento da **Lambda de Processamento**.
* **Gatilho:** Configuração da notificação S3 para invocar a Lambda automaticamente após o *upload* do arquivo JSON.

### **Consulta e API REST (Interface para o RH)**

* **Função de Consulta:** Criação da **Lambda de Consulta** para buscar os dados no DynamoDB.
* **API Gateway:** Provisionamento de um **API Gateway REST** que expõe o recurso `/curriculos` via método **GET**.
* **Integração:** Ligação do método GET diretamente à Lambda de Consulta, permitindo que o RH consulte os dados via URL.


## ** Conclusão do aprendizado

Este projeto demonstrou como construir uma arquitetura moderna que é, ao mesmo tempo, **poderosa** e **econômica**:

* **Arquitetura Desacoplada:** Separação do recebimento de dados (S3) do processamento (Lambda), garantindo alta escalabilidade.
* **Custo-Efetividade:** Uso de Lambda, onde o custo é apenas pelo tempo de execução, eliminando servidores ociosos.
* **Agilidade na Consulta:** API Gateway e DynamoDB garantem uma experiência de consulta de dados ultrarrápida.

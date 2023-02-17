# RabbitMQ
Simple App to send and recieve message on RabbitMQ

## Steps to run the project
1. Create RabbitMQ container in Docker with `docker-compose.yml up`.
2. Access RabbitMQ in http://localhost:15672 using username and password **guest**.
3. Run Send project (`dotnet run` or in your IDE).
4. Run Receive project.

## Anotações sobre o RabbitMQ

**Publisher** - publica conteúdo em uma Exchange.
**Consumer** - consome mensagem de uma fila.

A comunicação toda é feita via um Channel, e cada uma é uma thread separada.
Entre o publicador e a fila há o Exchange no RabbitMQ que manda mensagem para as filas. Kafka manda mensagem direto para fila.
No RabbitMQ as mensagens devem ser confirmadas, **ack** confirma e **reject** rejeita a mensagem (unacked) e pode ir para uma **DLQ (Dead Letter Queue)**.

### Exchanges

É necessário criar **Binds** entre **Exchange** e Filas (**Queue**) que determinem para qual fila vai a mensagem (Routing key).
Existem vários tipos de Exchanges:
1. **Direct**: manda mensagem direto para uma fila;
2. **Fanout**: manda mensagem para todas as filas com "Bind";
3. **Topic**: pode aplicar algumas regras customizadas para o envio de mensagens.
4. **Header**: usa o header para fazer roteamento das mensagens e ignora a routing key. Pode ser configurado para bater todos os parametros (x-match, all) ou pelo menos um (x-match, any)
5. **Default**: o RabbitMQ automaticamente envia mensagem para a queue que tenha o mesmo nome do que vier na **routing key**, sem precisar fazer bind.
6. **Alternate**: Pode ser usada para configurar uma outra exchange (funout) para receber todas as mensagens que não foram recebidas por nenhuma queue da exchange principal.

#### Topic Exchange
Usa **routing key** para rotear mensagens. Pode usar varias regras e partes de texto para filtrar as mensagens. O * é usado para pesquisar exatamente uma palavra, enquanto # filtra zero ou mais palavras. Exemplos:
- *.image = match com "convert.image", mas não com "convert.bitmap.image" ou "image.jpg"
- image# = match com imagem.jpg e image.bitmap.32bit mas não com convert.image

### Queue
Filas (Queues) são FIFO. **Durable** significa que ela fica salva mesmo com restart do broker. 
Pelos Bindings é possível ver quais Exchanges estão conectadas, com qual routing key e arguments. 
TTL é o tempo de vida da mensagem, que pode ser removida após x tempo.

### DLQ - Dead letter queues
 Mensagens que não foram entregues com sucesso podem ser configuradas para uma DLQ, que é outra fila que pode ser consumida para resolver problemas ocorridos na leitura da mensagem.
 
 ## RabbitMQ x Kafka
 - Kafka permite multiplos consumers de uma mesma mensagem, por padrão RabbbitMQ envia mensagem para apenas um consumer.
 - Kafka trabalha com mensagens strings ou binárias
 - RabbitMQ faz push das mensagens da fila para os consumers, no Kafka os consumers é que fazem pull das mensagens.
 - RabbitMQ remove as mensagens que são confirmadas como recebidas, já o Kafka armazena as mensagens como log.
 - RabbitMQ permite customizar roteamento das mensagens, Kafka só manda direto para o tópico.
 - O ideal é trabalhar com RabbitMQ para envio de **mensagens**, que são recados com poucas informações. Já no Kafka o ideal é usar para **stream de dados**, onde se tem fluxo de dados completos. Até dá para usar o Kafka como mensageria mas ele tem uma grande estrutura que acabaria não sendo necessária.
 - Kafka tem muitas outras opções como o **Kafka Connect** que faz integração de dados sem precisar programar.
 - Kafka de forma geral é um banco de dados imutável de logs.
 - Kafka pode ser comparado mais com AWS Kinesis e Apache Pulsar. Já RabbitMQ tem como concorrente o AWS SQS e Service Bus.

## Outros serviços de Mensagem da Microsoft

- **Event Grid** - Como Triggers de um banco, é usado para disparar ações a cada alteração.
- **Event Hubs** - Big Data pipeline, streaming de uma grande quantidade de mensagens, como em IOT.
- **Storage Queues** - Menos funcionalidades que o Service Bus mas suporta mais que 80gb.
- **Service Bus** - Para mensagens empresariais que tem valor de negócio, exemplo, aprovação de nota fiscal, troca de cartão de crédito. Quando a sequência é importante. Garante FIFO e tem muitas outras funcionalidades que o Storage Queues

## Service Bus

Serviço de barramento de mensagens da Microsoft baseado em uso de filas e tópicos.
Tudo é agrupado em uma namespace: Queues, Topics and Subscriptions.

### Queues (filas)

Filas armazenam mensagens até que uma aplicação esteja habilitada para receber e processar a mensagem.
A mensagem fica salva na cloud, sem que o Sender se preocupe se há um Receiver.
Uma fila é um armazenamento temporário para mensagens, pois a mensagens são entregue em modo pull. O Receiver é quem deve perguntar sobre a mensagem, não recebe automaticamente. As mensagens são entregue  em ordem FIFO.
É possível adicionar varias instâncias concorrentes de Receivers para poder escalar a aplicação.

### Topics e Subscriptions

Tópico é igual à fila porém ele pode ter múltiplas subscrições, assim a mesma mensagem pode ser entre para vários subscriptions, que são notificados quando há uma mensagem.       
A cada mensagem lida, os Receivers devem marca-la como Complete para que a mesma seja apagada da fila ou subscrição.

   
 Fontes: 
 - [RabbitMQ Documentation](https://www.rabbitmq.com/documentation.html) 
 - [RabbitMQ and Messaging Concepts](https://ambevtech.udemy.com/course-dashboard-redirect/?course_id=2526432)
 - [RabbitMQ - FullCycle](https://youtu.be/YotzziZzKJo)
 - [RabbitMQ vs Kafka - FullCycle](https://youtu.be/wq6v2ugPSDU) 
 - [Learn Azure Service Bus](https://www.linkedin.com/learning/azure-service-bus-19192316) 

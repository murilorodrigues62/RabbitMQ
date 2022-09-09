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
 - RabbitMQ permite customizar roteamento das mensagens, Kafka só manda direto para o tópico.+
   
 Fontes: 
 - [RabbitMQ Documentation](https://www.rabbitmq.com/documentation.html) 
 - [RabbitMQ and Messaging Concepts](https://ambevtech.udemy.com/course-dashboard-redirect/?course_id=2526432)
 - [RabbitMQ - FullCycle](https://youtu.be/YotzziZzKJo)
 - [RabbitMQ vs Kafka - FullCycle](https://www.youtube.com/watch?v=wq6v2ugPSDU) 


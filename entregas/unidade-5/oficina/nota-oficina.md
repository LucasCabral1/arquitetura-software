## Módulo 5 - Evidências: RabbitMQ e Consumidor Idempotente

### 1. Configuração Validada
**Comando executado:** `docker compose -f infra/compose.eventos.yml ps`

```text
NAME               IMAGE                   COMMAND                  SERVICE    STATUS                    PORTS
infra-rabbitmq-1   rabbitmq:4-management   "docker-entrypoint.s…"   rabbitmq   Up 3 seconds (healthy)    4369/tcp, 5671/tcp, 15671/tcp, 15691-15692/tcp, 25672/tcp, 0.0.0.0:15672->5672/tcp, [::]:15672->5672/tcp, 0.0.0.0:15673->15672/tcp, [::]:15673->15672/tcp
```

### 2. Saídas 
**Comando executado:** `py -m hospital.eventos.consumidor --once --store evidencias/modulo-5/processed-events.sqlite3` & `py -m hospital.eventos.consumidor --once --store evidencias/modulo-5/processed-events.sqlite3`

```text
ResultadoLaboratorialDisponibilizado.v1 event_id=3fa85f64-5717-4562-b3fc-2c963f66afa6 processed=True attempts=1
ResultadoLaboratorialDisponibilizado.v1 event_id=3fa85f64-5717-4562-b3fc-2c963f66afa6 processed=False attempts=2
```

### 3. Consulta de um efeito 
**Comando executado:** `py -c "import sqlite3; c=sqlite3.connect('evidencias/modulo-5/processed-events.sqlite3'); print(c.execute('select event_id, attempts from processed_events').fetchall()); print(c.execute('select count(*) from billing_effects').fetchone())"` 
```text
[('3fa85f64-5717-4562-b3fc-2c963f66afa6', 2)]
(1,)
```

### 4. Saídas 
**Comando executado:** `py -m hospital.eventos.publicador --event-id 65e95d82-4f8c-4e93-9bb3-3e0e92deaf1d --invalid` & `py -m hospital.eventos.consumidor --once --store evidencias/modulo-5/processed-events.sqlite3`

```text
Publicado: ResultadoLaboratorialDisponibilizado.v1 event_id=65e95d82-4f8c-4e93-9bb3-3e0e92deaf1d
Mensagem rejeitada para DLQ: schema inválido (1 erro)
```


### 5.  Explicação de por que há entrega pelo menos uma vez com idempotência
Garantir que o sistema entregue uma mensagem exatamente uma vez é quase impossível e trava a rede. Como a internet tem falhas, o sistema prefere pecar pelo excesso e entregar pelo menos uma vez.
Para que esse reenvio não gere cobranças duplicadas para o paciente, usamos a idempotência. Cada evento recebe um ID sintético único, quando a mensagem chega, o sistema anota esse ID. Se a mesma mensagem chegar de novo por causa de um reenvio da rede, o sistema reconhece o ID repetido, e ignora a cópia.

### 6. enário Kafka valeria como extensão do desenho atual?
O RabbitMQ é como uma lista de tarefas: assim que a mensagem é lida, ela é apagada.
O Kafka seria o complemento ideal caso o hospital precisasse guardar um histórico permanente



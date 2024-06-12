# Desafio Dio - Criando Uma API Para Jogo de Bingo Usando Spring WebFlux



### **Projeto Abrangente: Criando uma API para Jogo de Bingo Usando Spring WebFlux**



Spring WebFlux é um framework web assíncrono e não bloqueante para Java, construído sobre o projeto Reactor. Ele permite que os desenvolvedores criem APIs RESTful responsivas e escaláveis que podem lidar com cargas de trabalho pesadas.



### **Objetivos**

O objetivo deste projeto é demonstrar como usar o Spring WebFlux para criar uma API completa para um jogo de bingo. A API deve permitir que os jogadores:

- Crie e gerencie salas de bingo
- Participe de salas de bingo
- Receba atualizações em tempo real sobre o status do jogo
- Compre e use power-ups



### **Requisitos**

- Java 8 ou superior
- Spring Boot 2.x
- Spring WebFlux
- Banco de dados Mongo DB



### **Metodologia**



**1. Criar Modelo de Domínio**

Definir as entidades do domínio do jogo de bingo:

- **Sala:** Representa uma sala de bingo, com um nome, descrição e status.
- **Jogador:** Representa um jogador, com um nome, saldo e lista de power-ups.
- **Jogo:** Representa um jogo de bingo, com uma lista de números sorteados e um vencedor.
- **Power-Up:** Representa um power-up que pode ser comprado e usado pelos jogadores para melhorar suas chances de ganhar.



#### **2. Criar Repositórios**

Criar repositórios Spring Data para gerenciar as entidades do domínio:

java

```java
public interface RoomRepository extends CrudRepository<Room, Long> {}

public interface PlayerRepository extends CrudRepository<Player, Long> {}

public interface GameRepository extends CrudRepository<Game, Long> {}

public interface PowerUpRepository extends CrudRepository<PowerUp, Long> {}
```



#### **3. Criar Controladores**

Criar controladores Spring WebFlux para expor endpoints RESTful para CRUD entidades do domínio:



### **Criar uma Sala de Bingo**

java

```java
@PostMapping("/rooms")
public Mono<Room> createRoom(@RequestBody Room room) {
    return roomRepository.save(room);
}
```



### **Participar de uma Sala de Bingo**

java

```java
@PostMapping("/rooms/{roomId}/join")
public Mono<Player> joinRoom(@PathVariable Long roomId, @RequestBody Player player) {
    return roomRepository.findById(roomId)
        .flatMap(room -> {
            player.setRoom(room);
            return playerRepository.save(player);
        });
}
```



#### **4. Criar Serviço de Eventos**

Criar um serviço de eventos para publicar atualizações em tempo real sobre o status do jogo:

java

```java
public class EventService {

    private Flux<Event> events;

    public EventService() {
        this.events = Flux.create(sink -> {
            // Lógica para gerar e publicar eventos
        });
    }

    public Flux<Event> getEventsForRoom(Long roomId) {
        return events.filter(event -> event.getRoomId().equals(roomId));
    }
}
```



#### **5. Criar WebSockets**

Criar endpoints WebSocket para permitir que os jogadores se conectem e recebam atualizações em tempo real:

java

```java
@GetMapping("/rooms/{roomId}/events")
public Flux<Event> getEvents(@PathVariable Long roomId) {
    return eventService.getEventsForRoom(roomId);
}
```



#### **6. Adicionar Suporte a Power-Ups**

Adicionar suporte a power-ups criando uma nova entidade PowerUp e atualizando os controladores e serviços para permitir que os jogadores comprem e usem power-ups:



#### **Comprar um Power-Up**

java

```java
@PostMapping("/players/{playerId}/power-ups")
public Mono<PowerUp> buyPowerUp(@PathVariable Long playerId, @RequestBody PowerUp powerUp) {
    return playerRepository.findById(playerId)
        .flatMap(player -> {
            if (player.getBalance() >= powerUp.getPrice()) {
                player.addPowerUp(powerUp);
                player.setBalance(player.getBalance() - powerUp.getPrice());
                return playerRepository.save(player);
            } else {
                return Mono.error(new InsufficientBalanceException());
            }
        });
}
```



## Requisitos

- Gerenciar as informações dos jogadores (CRUD) com um find on demand;
- Gerar as cartelas de uma rodada com os números aleatórios, regras:
  - Todas as cartelas geradas devem ter quantidades iguais de números;
  - A cartela deve ter 20 números;
  - Uma cartela pode ter no máximo 1/4 dos mesmos números de uma outra cartela;
  - as cartelas da rodada só podem ser geradas antes de começar o sorteio dos números;
- Possibilidade de vincular uma cartela ao jogador ( 1 jogador só pode ser vinculado á uma cartela por rodada);
- Guardar um histórico das rodadas com os números sorteados, regras:
  - Cada rodada pode sortear números de 0 até 99;
  - Guardar os números sorteados;
  - Guardar as cartelas que pertencem a ela;
  - Guardar os jogadores que participaram;
- Endpoint para sortear o próximo número da rodada (um número não pode ser sorteado 2x na mesma rodada);
- Endpoint para buscar o último número sorteado;
- Cada vez que um número é sorteado deve-se verificar se alguma cartela já completou todos os números, caso tenha completado a rodada deve ser encerrada (bloquear geração de novos números) e um e-mail deve ser enviado ao vencedor da partida e os outros jogadores devem receber um e-mail mostrando como eles se sairam;
- Endpoint para buscar todas as rodadas (find all) o find on demand fica como opcional;
- Endpoint para buscar informações de uma partida pelo identificador
- Dockerizar a aplicação (opcional);
- Montar documentação dos endpoints (opcional);



## Requisitos de testes

- Os testes devem contemplar controller, services e repositórios que não são interfaces



## Dicas



### Sugestão de endpoints:



- Jogadores:
  - save (POST /players)
  
  - update (PUT /players/{id})
  
  - delete (DELETE /players/{id)
  
  - find by id (GET /players/{id})
  
  - find on demand (GET /players)
  
    
  
- ## Rodada:
  
  - criar rodada (POST /rounds)
  - gerar número (POST /rounds/{id}/generate-number)
  - buscar ultimo número sorteado (GET /rounds/{id}/current-number)
  - gerar cartela (POST /rounds/{id}/bingo-card/{playerId})
  - buscar rodadas (GET /rounds)
  - buscar rodada pelo id (GET /rounds/{id})



## Tecnologias

- Java 21
- Spring Framework with Spring Boot (3.3.0)
- Spring WebFlux (Reactive Web Rest API)
- MongoDB
- Docker



## Boas práticas

- Hexagonal Architecture



## Bibliotecas utilizadas

- spring-boot-starter-webflux
- spring-boot-starter-data-mongodb-reactive
- spring-boot-starter-validation
- spring-boot-starter-mail
- spring-boot-starter-thymeleaf
- spring-boot-starter-test
- reactor-test
- lombok
- mapstruct
- mapstruct-processor
- lombok-mapstruct-binding
- commons-lang3
- springdoc-openapi-starter-webflux-ui



## **Conclusão**



Esta API abrangente para um jogo de bingo demonstra o poder do Spring WebFlux para criar APIs RESTful responsivas e escaláveis. Ao utilizar o Spring WebFlux, os desenvolvedores podem criar aplicativos que podem lidar com cargas de trabalho pesadas e fornecer experiências em tempo real para os usuários.

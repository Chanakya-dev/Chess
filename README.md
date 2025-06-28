## ♟️ Project: **Multiplayer Chess Game Platform**

### 🔧 Microservices & Their Responsibilities

---

### 1. 🧑‍💼 **UserService**

**Responsibilities:**

* User registration & login
* JWT token generation & validation (if not handled at Gateway)
* Profile info, ELO rating system
* Store match history
* Provide player stats

**Endpoints:**

* `POST /users/register`
* `POST /users/login`
* `GET /users/{id}/profile`
* `GET /users/{id}/history`

---

### 2. ♟️ **GameService**

**Responsibilities:**

* Validate chess moves based on current board state
* Checkmate/stalemate detection
* Manage turn order, timers (optional)
* Track game state (in-memory or Redis)
* Communicate with SocketService

**Endpoints:**

* `POST /games/validateMove`
* `GET /games/{id}/status`
* `POST /games/saveResult`

---

### 3. 🔌 **SocketService**

**Responsibilities:**

* Handle real-time player connections (Java Sockets)
* Create game rooms, pair players in rooms
* Start a thread per game or per player
* Route messages (moves) between players
* Call `GameService` to validate moves and fetch updates

**Core Logic:**

* TCP socket server
* `ExecutorService` for multithreading
* Maintain active sessions in memory

---

### 4. 🤝 **MatchmakerService**

**Responsibilities:**

* Match available players into a game room
* Create new game rooms dynamically
* Notify players via socket or REST
* Optionally queue players (using Redis or in-memory map)

**Endpoints:**

* `POST /match/join`
* `GET /match/status/{userId}`
* `POST /match/cancel`

---

### 5. 🚪 **GatewayService (API Gateway)**

**Responsibilities:**

* Central entry point for all HTTP clients
* Authenticate JWT tokens (authentication filter)
* Route requests to appropriate services
* Handle rate-limiting, logging, etc.

**Config Examples (Spring Cloud Gateway):**

```yaml
spring.cloud.gateway.routes:
  - id: user-service
    uri: lb://USER-SERVICE
    predicates:
      - Path=/users/**
  - id: game-service
    uri: lb://GAME-SERVICE
    predicates:
      - Path=/games/**
```

---

### 6. 📊 **Optional: LeaderboardService**

**Responsibilities:**

* Maintain rankings based on ELO or win count
* Show global/player-specific rankings

**Endpoints:**

* `GET /leaderboard/global`
* `GET /leaderboard/user/{id}`

---

## ✅ Optional Tools & Add-ons

| Tool                     | Use Case                                     |
| ------------------------ | -------------------------------------------- |
| **Kafka**                | Communicate between Matchmaker ↔ GameService |
| **Redis**                | Store active game state or matchmaking queue |
| **MySQL**                | Store users, game history, profiles          |
| **Docker Compose**       | Run all services locally for development     |
| **Prometheus + Grafana** | Monitor service health and game stats        |

---

## 📌 Summary Table

| Service Name                    | Core Functionality                          |
| ------------------------------- | ------------------------------------------- |
| `UserService`                   | Auth, profile, rating, match history        |
| `GameService`                   | Game logic, move validation, board state    |
| `SocketService`                 | Real-time socket server with multithreading |
| `MatchmakerService`             | Matchmaking, game room assignment           |
| `GatewayService`                | Authentication, routing, access control     |
| `LeaderboardService` (optional) | Global rankings, user leaderboard           |

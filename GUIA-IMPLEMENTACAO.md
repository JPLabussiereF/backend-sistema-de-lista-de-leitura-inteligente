# Backend - Sistema de Lista de Leitura Inteligente

## 📋 Visão Geral
Sistema backend em Java para gerenciar listas de leitura, recomendar livros por tema e acompanhar progresso de estudos através da integração com Google Books API.

---

## 🏗️ Arquitetura de Pastas

```
reading-list-api/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── readinglist/
│   │   │           ├── ReadingListApplication.java
│   │   │           ├── config/
│   │   │           │   ├── SecurityConfig.java
│   │   │           │   ├── GoogleBooksConfig.java
│   │   │           │   └── CorsConfig.java
│   │   │           ├── controller/
│   │   │           │   ├── BookController.java
│   │   │           │   ├── ReadingListController.java
│   │   │           │   ├── LearningPathController.java
│   │   │           │   ├── ProgressController.java
│   │   │           │   └── RecommendationController.java
│   │   │           ├── service/
│   │   │           │   ├── BookService.java
│   │   │           │   ├── GoogleBooksService.java
│   │   │           │   ├── ReadingListService.java
│   │   │           │   ├── LearningPathService.java
│   │   │           │   ├── ProgressService.java
│   │   │           │   └── RecommendationService.java
│   │   │           ├── repository/
│   │   │           │   ├── BookRepository.java
│   │   │           │   ├── ReadingListRepository.java
│   │   │           │   ├── LearningPathRepository.java
│   │   │           │   ├── UserBookRepository.java
│   │   │           │   └── ReadingSessionRepository.java
│   │   │           ├── model/
│   │   │           │   ├── entity/
│   │   │           │   │   ├── Book.java
│   │   │           │   │   ├── ReadingList.java
│   │   │           │   │   ├── LearningPath.java
│   │   │           │   │   ├── UserBook.java
│   │   │           │   │   ├── ReadingSession.java
│   │   │           │   │   └── Category.java
│   │   │           │   ├── dto/
│   │   │           │   │   ├── BookDTO.java
│   │   │           │   │   ├── ReadingListDTO.java
│   │   │           │   │   ├── LearningPathDTO.java
│   │   │           │   │   ├── ProgressDTO.java
│   │   │           │   │   ├── RecommendationDTO.java
│   │   │           │   │   └── GoogleBooksResponseDTO.java
│   │   │           │   └── enums/
│   │   │           │       ├── ReadingStatus.java
│   │   │           │       ├── Priority.java
│   │   │           │       └── KnowledgeArea.java
│   │   │           ├── exception/
│   │   │           │   ├── GlobalExceptionHandler.java
│   │   │           │   ├── BookNotFoundException.java
│   │   │           │   ├── GoogleBooksApiException.java
│   │   │           │   └── InvalidDataException.java
│   │   │           └── util/
│   │   │               ├── DateUtil.java
│   │   │               ├── ProgressCalculator.java
│   │   │               └── BookMapper.java
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── application-dev.properties
│   │       ├── application-prod.properties
│   │       └── db/
│   │           └── migration/
│   │               ├── V1__create_initial_tables.sql
│   │               ├── V2__add_reading_session_table.sql
│   │               ├── V3__add_learning_path_tables.sql
│   │               └── V4__add_indexes.sql
│   └── test/
│       └── java/
│           └── com/
│               └── readinglist/
│                   ├── service/
│                   ├── controller/
│                   └── integration/
├── pom.xml
└── README.md
```

---

## 🎯 Funcionalidades Detalhadas

### 1. **Gestão de Livros**

#### 1.1 Buscar Livros (Google Books API)
- **Endpoint:** `GET /api/books/search?query={termo}&area={area}&maxResults={n}`
- **Descrição:** Busca livros na API do Google Books
- **Parâmetros:**
  - `query`: termo de busca
  - `area`: área de conhecimento (opcional)
  - `maxResults`: quantidade de resultados (padrão: 10)
- **Retorno:** Lista de livros com título, autor, descrição, páginas, capa, categorias, avaliação média

#### 1.2 Detalhes do Livro
- **Endpoint:** `GET /api/books/{googleBooksId}`
- **Descrição:** Obtém informações completas de um livro específico
- **Retorno:** Detalhes completos incluindo preview links, editora, ISBN, data de publicação

#### 1.3 Adicionar Livro ao Sistema
- **Endpoint:** `POST /api/books`
- **Descrição:** Salva um livro do Google Books no banco de dados local
- **Body:** GoogleBooksId + notas personalizadas (opcional)

---

### 2. **Lista de Leitura**

#### 2.1 Criar Lista de Leitura
- **Endpoint:** `POST /api/reading-lists`
- **Descrição:** Cria uma nova lista de leitura
- **Body:**
  ```json
  {
    "name": "Neurociência 2024",
    "description": "Livros sobre neurociência que quero ler",
    "knowledgeArea": "NEUROCIENCIA"
  }
  ```

#### 2.2 Adicionar Livro à Lista
- **Endpoint:** `POST /api/reading-lists/{listId}/books`
- **Descrição:** Adiciona um livro a uma lista específica
- **Body:**
  ```json
  {
    "bookId": 123,
    "priority": "HIGH",
    "status": "TO_READ",
    "notes": "Recomendado pelo podcast XYZ"
  }
  ```

#### 2.3 Atualizar Status do Livro
- **Endpoint:** `PATCH /api/reading-lists/{listId}/books/{bookId}`
- **Descrição:** Atualiza status (TO_READ, READING, COMPLETED)
- **Body:**
  ```json
  {
    "status": "READING",
    "currentPage": 45
  }
  ```

#### 2.4 Listar Todas as Listas
- **Endpoint:** `GET /api/reading-lists`
- **Descrição:** Retorna todas as listas do usuário com contagem de livros por status

#### 2.5 Obter Livros de uma Lista
- **Endpoint:** `GET /api/reading-lists/{listId}/books?status={status}&sortBy={field}`
- **Descrição:** Lista livros com filtros e ordenação
- **Parâmetros:**
  - `status`: filtrar por status (opcional)
  - `sortBy`: ordenar por priority, dateAdded, title (opcional)

---

### 3. **Trilhas de Aprendizado**

#### 3.1 Criar Trilha de Aprendizado
- **Endpoint:** `POST /api/learning-paths`
- **Descrição:** Cria uma trilha com objetivo de estudo
- **Body:**
  ```json
  {
    "name": "Master em JavaScript",
    "description": "Do básico ao avançado",
    "knowledgeArea": "TECNOLOGIA",
    "estimatedMonths": 6
  }
  ```

#### 3.2 Adicionar Livros à Trilha (Ordem Sequencial)
- **Endpoint:** `POST /api/learning-paths/{pathId}/books`
- **Descrição:** Adiciona livros em ordem de estudo
- **Body:**
  ```json
  {
    "bookId": 456,
    "orderInPath": 1,
    "isOptional": false,
    "estimatedDays": 30
  }
  ```

#### 3.3 Listar Trilhas
- **Endpoint:** `GET /api/learning-paths`
- **Descrição:** Retorna todas as trilhas com progresso geral

#### 3.4 Detalhes da Trilha
- **Endpoint:** `GET /api/learning-paths/{pathId}`
- **Descrição:** Retorna trilha completa com todos os livros em ordem e progresso individual

---

### 4. **Progresso de Estudos**

#### 4.1 Registrar Sessão de Leitura
- **Endpoint:** `POST /api/progress/sessions`
- **Descrição:** Registra uma sessão de leitura
- **Body:**
  ```json
  {
    "bookId": 789,
    "pagesRead": 25,
    "date": "2024-12-03",
    "durationMinutes": 45,
    "notes": "Capítulo sobre padrões de design muito interessante"
  }
  ```

#### 4.2 Obter Progresso de um Livro
- **Endpoint:** `GET /api/progress/books/{bookId}`
- **Descrição:** Retorna progresso detalhado (páginas lidas, % concluído, histórico de sessões)

#### 4.3 Dashboard de Progresso Geral
- **Endpoint:** `GET /api/progress/dashboard?period={period}`
- **Descrição:** Retorna estatísticas gerais
- **Retorno:**
  ```json
  {
    "totalBooksCompleted": 15,
    "totalPagesRead": 3240,
    "averagePagesPerDay": 12.5,
    "currentStreak": 7,
    "longestStreak": 21,
    "readingTimeMinutes": 1850,
    "booksInProgress": 3,
    "progressByArea": {
      "TECNOLOGIA": 45.2,
      "NEUROCIENCIA": 23.8
    }
  }
  ```

#### 4.4 Progresso Mensal
- **Endpoint:** `GET /api/progress/monthly?year={year}&month={month}`
- **Descrição:** Páginas lidas por dia do mês (para gráfico)

#### 4.5 Tempo Estimado para Conclusão
- **Endpoint:** `GET /api/progress/books/{bookId}/estimate`
- **Descrição:** Calcula tempo estimado baseado na velocidade de leitura média do usuário

---

### 5. **Recomendações**

#### 5.1 Recomendar por Tema
- **Endpoint:** `GET /api/recommendations/by-topic?topic={tema}&limit={n}`
- **Descrição:** Busca livros relacionados a um tema específico
- **Exemplo:** `topic=neuroplasticidade` retorna livros sobre o assunto

#### 5.2 Recomendar Próximo Livro
- **Endpoint:** `GET /api/recommendations/next?area={area}`
- **Descrição:** Sugere próximo livro baseado em:
  - Área de conhecimento atual
  - Livros já lidos
  - Avaliações do Google Books
  - Livros populares da área

#### 5.3 Livros Relacionados
- **Endpoint:** `GET /api/recommendations/related/{bookId}`
- **Descrição:** Encontra livros similares baseado em categorias e autores

#### 5.4 Completar Bibliografia de um Tópico
- **Endpoint:** `POST /api/recommendations/build-bibliography`
- **Descrição:** Cria uma lista abrangente sobre um assunto
- **Body:**
  ```json
  {
    "topic": "Clean Architecture",
    "depth": "COMPREHENSIVE",
    "includeClassics": true,
    "includeRecent": true
  }
  ```

---

## 🗄️ Modelo de Dados

### Book
```java
- id: Long
- googleBooksId: String (único)
- title: String
- authors: List<String>
- description: String
- pageCount: Integer
- publisher: String
- publishedDate: LocalDate
- categories: List<String>
- averageRating: Double
- imageUrl: String
- previewLink: String
- infoLink: String
- createdAt: LocalDateTime
```

### ReadingList
```java
- id: Long
- name: String
- description: String
- knowledgeArea: KnowledgeArea (enum)
- createdAt: LocalDateTime
- updatedAt: LocalDateTime
```

### UserBook (relacionamento livro-lista)
```java
- id: Long
- book: Book (ManyToOne)
- readingList: ReadingList (ManyToOne)
- status: ReadingStatus (TO_READ, READING, COMPLETED)
- priority: Priority (LOW, MEDIUM, HIGH)
- currentPage: Integer
- notes: String
- dateAdded: LocalDateTime
- dateStarted: LocalDateTime
- dateCompleted: LocalDateTime
```

### LearningPath
```java
- id: Long
- name: String
- description: String
- knowledgeArea: KnowledgeArea
- estimatedMonths: Integer
- createdAt: LocalDateTime
- books: List<PathBook> (ordenado)
```

### PathBook
```java
- id: Long
- learningPath: LearningPath
- book: Book
- orderInPath: Integer
- isOptional: Boolean
- estimatedDays: Integer
- completed: Boolean
```

### ReadingSession
```java
- id: Long
- userBook: UserBook
- date: LocalDate
- pagesRead: Integer
- durationMinutes: Integer
- notes: String
```

### Enums

**ReadingStatus:** TO_READ, READING, COMPLETED, ABANDONED

**Priority:** LOW, MEDIUM, HIGH

**KnowledgeArea:** TECNOLOGIA, NEUROCIENCIA, COMPORTAMENTO, ENGENHARIA_SOFTWARE, DESIGN_PATTERNS, ARQUITETURA, CIENCIA_DADOS, OUTROS

---

## 🔧 Tecnologias e Dependências

### Core
- **Java 17+**
- **Spring Boot 3.x**
- **Spring Web** (REST API)
- **Spring Data JPA** (Persistência)
- **Spring Validation** (Validação de dados)

### Banco de Dados
- **PostgreSQL** (produção)
- **H2** (desenvolvimento/testes)

### Integrações
- **RestTemplate/WebClient** (chamadas à Google Books API)
- **Jackson** (JSON parsing)

### Migrations
- **Flyway** (controle de versão do banco de dados)

### Utilitários
- **Lombok** (reduzir boilerplate)
- **MapStruct** (mapeamento Entity ↔ DTO)
- **ModelMapper** (alternativa ao MapStruct)

### Testes
- **JUnit 5**
- **Mockito**
- **MockMvc** (testes de controller)
- **Testcontainers** (testes de integração com PostgreSQL)

### Documentação
- **SpringDoc OpenAPI** (Swagger UI)

---

## ⚙️ Configurações Importantes

### application.properties
```properties
# Google Books API
google.books.api.url=https://www.googleapis.com/books/v1
google.books.api.key=${GOOGLE_BOOKS_API_KEY}

# Database
spring.datasource.url=jdbc:postgresql://localhost:5432/readinglist
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

# JPA - IMPORTANTE: usar validate em produção com Flyway
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=true

# Flyway
spring.flyway.enabled=true
spring.flyway.baseline-on-migrate=true
spring.flyway.locations=classpath:db/migration

# API Settings
server.port=8080
spring.application.name=reading-list-api
```

---

## 🗄️ Migrations com Flyway

### Por que usar Flyway?

1. **Versionamento:** Cada alteração no banco fica documentada e versionada
2. **Rastreabilidade:** Sabe exatamente quais migrations foram aplicadas
3. **Consistência:** Garante que dev, staging e produção tenham o mesmo schema
4. **Segurança:** Evita usar `ddl-auto=update` em produção (que pode dar problema)
5. **Rollback facilitado:** Pode criar migrations de reversão
6. **Trabalho em equipe:** Evita conflitos de schema entre desenvolvedores

### Estrutura dos Arquivos de Migration

Os arquivos devem seguir o padrão: `V{versão}__{descrição}.sql`

Exemplo: `V1__create_initial_tables.sql`

**Localização:** `src/main/resources/db/migration/`

### Exemplo de Migrations

#### V1__create_initial_tables.sql
```sql
-- Tabela de categorias/áreas de conhecimento
CREATE TABLE knowledge_areas (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT
);

-- Tabela de livros
CREATE TABLE books (
    id BIGSERIAL PRIMARY KEY,
    google_books_id VARCHAR(255) NOT NULL UNIQUE,
    title VARCHAR(500) NOT NULL,
    authors TEXT, -- JSON ou texto separado por vírgula
    description TEXT,
    page_count INTEGER,
    publisher VARCHAR(255),
    published_date DATE,
    categories TEXT, -- JSON ou texto separado por vírgula
    average_rating DECIMAL(3,2),
    image_url VARCHAR(1000),
    preview_link VARCHAR(1000),
    info_link VARCHAR(1000),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Tabela de listas de leitura
CREATE TABLE reading_lists (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    knowledge_area VARCHAR(50) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Tabela de relacionamento usuário-livro-lista
CREATE TABLE user_books (
    id BIGSERIAL PRIMARY KEY,
    book_id BIGINT NOT NULL REFERENCES books(id) ON DELETE CASCADE,
    reading_list_id BIGINT NOT NULL REFERENCES reading_lists(id) ON DELETE CASCADE,
    status VARCHAR(20) NOT NULL DEFAULT 'TO_READ',
    priority VARCHAR(20) NOT NULL DEFAULT 'MEDIUM',
    current_page INTEGER DEFAULT 0,
    notes TEXT,
    date_added TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    date_started TIMESTAMP,
    date_completed TIMESTAMP,
    CONSTRAINT fk_book FOREIGN KEY (book_id) REFERENCES books(id),
    CONSTRAINT fk_reading_list FOREIGN KEY (reading_list_id) REFERENCES reading_lists(id),
    CONSTRAINT chk_status CHECK (status IN ('TO_READ', 'READING', 'COMPLETED', 'ABANDONED')),
    CONSTRAINT chk_priority CHECK (priority IN ('LOW', 'MEDIUM', 'HIGH'))
);
```

#### V2__add_reading_session_table.sql
```sql
-- Tabela de sessões de leitura
CREATE TABLE reading_sessions (
    id BIGSERIAL PRIMARY KEY,
    user_book_id BIGINT NOT NULL REFERENCES user_books(id) ON DELETE CASCADE,
    session_date DATE NOT NULL,
    pages_read INTEGER NOT NULL,
    duration_minutes INTEGER,
    notes TEXT,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_user_book FOREIGN KEY (user_book_id) REFERENCES user_books(id),
    CONSTRAINT chk_pages_positive CHECK (pages_read > 0)
);

-- Índice para consultas por data
CREATE INDEX idx_reading_sessions_date ON reading_sessions(session_date);
CREATE INDEX idx_reading_sessions_user_book ON reading_sessions(user_book_id);
```

#### V3__add_learning_path_tables.sql
```sql
-- Tabela de trilhas de aprendizado
CREATE TABLE learning_paths (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    knowledge_area VARCHAR(50) NOT NULL,
    estimated_months INTEGER,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Tabela de livros em trilhas (relacionamento ordenado)
CREATE TABLE path_books (
    id BIGSERIAL PRIMARY KEY,
    learning_path_id BIGINT NOT NULL REFERENCES learning_paths(id) ON DELETE CASCADE,
    book_id BIGINT NOT NULL REFERENCES books(id) ON DELETE CASCADE,
    order_in_path INTEGER NOT NULL,
    is_optional BOOLEAN NOT NULL DEFAULT FALSE,
    estimated_days INTEGER,
    completed BOOLEAN NOT NULL DEFAULT FALSE,
    completed_at TIMESTAMP,
    CONSTRAINT fk_learning_path FOREIGN KEY (learning_path_id) REFERENCES learning_paths(id),
    CONSTRAINT fk_path_book FOREIGN KEY (book_id) REFERENCES books(id),
    CONSTRAINT uq_path_order UNIQUE (learning_path_id, order_in_path)
);

-- Índices para performance
CREATE INDEX idx_path_books_path ON path_books(learning_path_id);
CREATE INDEX idx_path_books_order ON path_books(learning_path_id, order_in_path);
```

#### V4__add_indexes.sql
```sql
-- Índices para melhorar performance de consultas frequentes
CREATE INDEX idx_books_google_id ON books(google_books_id);
CREATE INDEX idx_books_title ON books(title);
CREATE INDEX idx_user_books_status ON user_books(status);
CREATE INDEX idx_user_books_priority ON user_books(priority);
CREATE INDEX idx_user_books_list ON user_books(reading_list_id);
CREATE INDEX idx_reading_lists_area ON reading_lists(knowledge_area);
CREATE INDEX idx_learning_paths_area ON learning_paths(knowledge_area);
```

### Como Funciona o Flyway

1. **Na primeira execução:** Flyway cria uma tabela `flyway_schema_history` que registra todas as migrations aplicadas
2. **Em execuções subsequentes:** Flyway verifica quais migrations já foram aplicadas e executa apenas as novas
3. **Ordem de execução:** Migrations são executadas em ordem numérica (V1, V2, V3...)
4. **Imutabilidade:** Uma vez aplicada, a migration não deve ser alterada (crie uma nova se precisar mudar algo)

### Comandos Úteis do Flyway

```bash
# Aplicar todas as migrations pendentes
mvn flyway:migrate

# Ver status das migrations
mvn flyway:info

# Limpar o banco (CUIDADO - apenas em dev!)
mvn flyway:clean

# Validar migrations
mvn flyway:validate
```

### Boas Práticas com Migrations

1. **Nunca altere uma migration já aplicada** - crie uma nova migration para correções
2. **Teste em desenvolvimento antes** de aplicar em produção
3. **Use nomes descritivos** para as migrations
4. **Mantenha migrations pequenas** - uma alteração por migration quando possível
5. **Dados de teste** - crie migrations separadas para dados de exemplo (V999__insert_test_data.sql)
6. **Rollback** - se precisar reverter, crie uma nova migration que desfaz a anterior
7. **Backup** - sempre faça backup antes de aplicar migrations em produção

### Exemplo de Migration de Dados

#### V5__insert_default_knowledge_areas.sql
```sql
-- Inserir áreas de conhecimento padrão
INSERT INTO knowledge_areas (name, description) VALUES
('TECNOLOGIA', 'Livros sobre tecnologia, programação e desenvolvimento'),
('NEUROCIENCIA', 'Livros sobre neurociência, cérebro e cognição'),
('COMPORTAMENTO', 'Livros sobre comportamento humano e psicologia'),
('ENGENHARIA_SOFTWARE', 'Livros sobre engenharia de software e arquitetura'),
('DESIGN_PATTERNS', 'Livros sobre padrões de design e boas práticas'),
('ARQUITETURA', 'Livros sobre arquitetura de software e sistemas'),
('CIENCIA_DADOS', 'Livros sobre ciência de dados, ML e AI'),
('OUTROS', 'Outros temas e áreas de conhecimento');
```

### Configuração no pom.xml

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
```

---

## 📝 Ordem de Implementação Sugerida

1. **Setup inicial do projeto + configuração de banco**
2. **Configurar Flyway e criar primeira migration (tabelas principais)**
3. **Model (entidades e enums)**
4. **Repository (interfaces JPA)**
5. **GoogleBooksService (integração com API externa)**
6. **BookService + BookController (CRUD básico)**
7. **ReadingListService + Controller**
8. **Funcionalidade de adicionar livros à lista**
9. **Criar migrations para tabelas de trilhas (V3)**
10. **LearningPathService + Controller**
11. **Criar migration para tabela de sessões (V2)**
12. **ProgressService + ReadingSession**
13. **Dashboard de progresso e estatísticas**
14. **RecommendationService (lógica de recomendações)**
15. **Criar migration de índices (V4)**
16. **Testes unitários e de integração**
17. **Documentação Swagger**

---

## 🚀 Endpoints Resumidos

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | /api/books/search | Buscar livros no Google Books |
| GET | /api/books/{id} | Detalhes de um livro |
| POST | /api/books | Adicionar livro ao sistema |
| POST | /api/reading-lists | Criar lista de leitura |
| GET | /api/reading-lists | Listar todas as listas |
| POST | /api/reading-lists/{id}/books | Adicionar livro à lista |
| PATCH | /api/reading-lists/{listId}/books/{bookId} | Atualizar status |
| POST | /api/learning-paths | Criar trilha de aprendizado |
| GET | /api/learning-paths | Listar trilhas |
| POST | /api/learning-paths/{id}/books | Adicionar livro à trilha |
| POST | /api/progress/sessions | Registrar sessão de leitura |
| GET | /api/progress/dashboard | Dashboard geral |
| GET | /api/progress/books/{id} | Progresso de um livro |
| GET | /api/recommendations/by-topic | Recomendar por tema |
| GET | /api/recommendations/next | Sugerir próximo livro |

---

## 💡 Funcionalidades Extras (Futuro)

- Exportar listas em PDF
- Compartilhar listas com outros usuários
- Metas de leitura (ex: 50 livros por ano)
- Integração com Goodreads
- Notificações de lembretes de leitura
- Sistema de tags customizadas
- Anotações e highlights por livro
- Gráficos de velocidade de leitura ao longo do tempo

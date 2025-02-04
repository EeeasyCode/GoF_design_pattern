# 생성 패턴(Creational Pattern)

객체 생성의 유연성을 극대화하는 설계 패턴

소프트웨어 설계에서 객체 생성은 단순한 작업처럼 보일 수 있지만, 실제로는 유지보수와 확장성에 큰 영향을 미침. **생성 패턴(Creational Pattern)**은 객체 생성 과정을 추상화하여 유연하고 확장 가능한 구조를 제공하는 설계 방법임.

이번 정리에서는 생성 패턴의 주요 종류와 각 패턴의 특징 및 예제를 소개함.

## 생성 패턴의 주요 종류

생성 패턴은 총 5가지로, 각각의 패턴은 객체 생성 과정을 추상화하고 체계적으로 관리하는 데 도움을 줌.

### 1. 팩토리 메서드 패턴 (Factory Method Pattern)

객체 생성의 책임을 서브클래스에 위임하는 패턴

**특징**

- 부모 클래스는 객체 생성 인터페이스를 정의하고, 구체적인 객체 생성은 서브클래스에서 처리함.
- 객체 생성 로직의 캡슐화로 유연성을 제공함.

**예제: 미로의 벽 종류를 다르게 생성**

```typescript
abstract class Maze {
  abstract createWall(): string;
}

class StoneMaze extends Maze {
  createWall(): string {
    return "Stone Wall";
  }
}

class WoodenMaze extends Maze {
  createWall(): string {
    return "Wooden Wall";
  }
}

// 사용 예
const maze: Maze = new StoneMaze();
console.log(maze.createWall()); // Stone Wall
```

### 2. 추상 팩토리 패턴 (Abstract Factory Pattern)

관련 객체의 그룹(제품군)을 생성하는 인터페이스 제공

**특징**

- 여러 제품군 객체의 생성을 하나의 팩토리에서 처리하며, 각 제품군 간의 의존성을 유지함.
- 제품군을 교체하거나 확장하기 쉽게 설계됨.

**예제: 데이터베이스 연결 관리**

```typescript
// Abstract Factory
interface DatabaseFactory {
  createConnection(): DatabaseConnection;
  createQueryExecutor(): QueryExecutor;
}

// Concrete Factory 1: MySQL
class MySQLFactory implements DatabaseFactory {
  createConnection(): DatabaseConnection {
    return new MySQLConnection();
  }
  createQueryExecutor(): QueryExecutor {
    return new MySQLQueryExecutor();
  }
}

// Concrete Factory 2: MongoDB
class MongoDBFactory implements DatabaseFactory {
  createConnection(): DatabaseConnection {
    return new MongoDBConnection();
  }
  createQueryExecutor(): QueryExecutor {
    return new MongoDBQueryExecutor();
  }
}

// Abstract Products
interface DatabaseConnection {
  connect(): void;
}
interface QueryExecutor {
  executeQuery(query: string): void;
}

// Concrete Products for MySQL
class MySQLConnection implements DatabaseConnection {
  connect(): void {
    console.log("Connected to MySQL");
  }
}
class MySQLQueryExecutor implements QueryExecutor {
  executeQuery(query: string): void {
    console.log(`Executing MySQL query: ${query}`);
  }
}

// Concrete Products for MongoDB
class MongoDBConnection implements DatabaseConnection {
  connect(): void {
    console.log("Connected to MongoDB");
  }
}
class MongoDBQueryExecutor implements QueryExecutor {
  executeQuery(query: string): void {
    console.log(`Executing MongoDB query: ${query}`);
  }
}

// 사용 예
const factory: DatabaseFactory = new MySQLFactory();
const connection = factory.createConnection();
const executor = factory.createQueryExecutor();

connection.connect(); // Output: Connected to MySQL
executor.executeQuery("SELECT * FROM users"); // Output: Executing MySQL query: SELECT * FROM users
```

### 3. 빌더 패턴 (Builder Pattern)

복합 객체를 단계적으로 생성하는 패턴

**특징**

- 생성 절차를 단계별로 정의하여, 동일한 생성 과정을 통해 서로 다른 표현의 객체를 생성할 수 있음.
- 복잡한 객체를 생성하는 데 유용함.

**예제: 이메일 생성**

```typescript
class Email {
  to: string = "";
  from: string = "";
  subject: string = "";
  body: string = "";

  display(): void {
    console.log(`
      To: ${this.to}
      From: ${this.from}
      Subject: ${this.subject}
      Body: ${this.body}
    `);
  }
}

interface EmailBuilder {
  setTo(to: string): this;
  setFrom(from: string): this;
  setSubject(subject: string): this;
  setBody(body: string): this;
  build(): Email;
}

class ConcreteEmailBuilder implements EmailBuilder {
  private email: Email;

  constructor() {
    this.email = new Email();
  }

  setTo(to: string): this {
    this.email.to = to;
    return this;
  }

  setFrom(from: string): this {
    this.email.from = from;
    return this;
  }

  setSubject(subject: string): this {
    this.email.subject = subject;
    return this;
  }

  setBody(body: string): this {
    this.email.body = body;
    return this;
  }

  build(): Email {
    return this.email;
  }
}

// 사용 예
const emailBuilder = new ConcreteEmailBuilder();
const email = emailBuilder
  .setTo("receiver@example.com")
  .setFrom("sender@example.com")
  .setSubject("Welcome!")
  .setBody("Thank you for joining us!")
  .build();

email.display();
```

### 4. 원형 패턴 (Prototype Pattern)

기존 객체를 복사하여 새로운 객체를 생성하는 패턴

**특징**

- 복제를 통해 객체를 생성하며, 새로운 객체 초기화에 드는 비용을 줄일 수 있음.
- 캐싱된 객체를 재사용하기에 유용함.

**예제: 설정 객체 복제**

```typescript
interface Cacheable {
  clone(): Cacheable;
}

class Config implements Cacheable {
  constructor(public settings: Record<string, any>) {}

  clone(): Config {
    return new Config({ ...this.settings });
  }
}

// 사용 예
const originalConfig = new Config({ theme: "dark", language: "en" });
const clonedConfig = originalConfig.clone();

console.log(originalConfig.settings); // { theme: "dark", language: "en" }
console.log(clonedConfig.settings); // { theme: "dark", language: "en" }
```

### 5. 싱글톤 패턴 (Singleton Pattern)

애플리케이션에서 클래스의 인스턴스를 단 하나만 보장하는 패턴

**특징**

- 애플리케이션 전역에서 공통된 리소스를 관리할 때 사용됨.
- 단일 인스턴스를 제공하며, 이를 전역적으로 접근 가능하게 만듦.

**예제: 데이터베이스 연결 관리**

```typescript
class Database {
  private static instance: Database;

  private constructor() {
    console.log("Initializing database connection...");
  }

  static getInstance(): Database {
    if (!Database.instance) {
      Database.instance = new Database();
    }
    return Database.instance;
  }

  query(sql: string): void {
    console.log(`Executing query: ${sql}`);
  }
}

// 사용 예
const db1 = Database.getInstance();
db1.query("SELECT * FROM users");

const db2 = Database.getInstance();
db2.query("SELECT * FROM orders");

console.log(db1 === db2); // Output: true
```

## 결론

생성 패턴은 객체 생성 과정을 체계적으로 관리하며, 코드의 유지보수성과 확장성을 극대화함.

- **팩토리 메서드 패턴**: 객체 생성의 책임을 서브클래스에 위임.
- **추상 팩토리 패턴**: 제품군 생성 및 의존성 분리.
- **빌더 패턴**: 복잡한 객체 단계적 생성.
- **원형 패턴**: 객체 복제 및 초기화 간소화.
- **싱글톤 패턴**: 전역적으로 공유되는 인스턴스 관리.

적절한 생성 패턴을 활용하면 복잡한 시스템에서도 유연하고 확장 가능한 설계를 구현할 수 있음.

---

실제로 최근에 진행한 프로젝트에서 이런 저런 디자인 패턴 (특히, 생성 패턴)을 도입하기 위한 고민을 많이 했었는데 그 중 몇 가지 고민을 공유해보려고 한다.

1. 추상클래스와 인터페이스는 언제, 왜 사용해야 할까?
2. 확장성이나 유연성을 위한 패턴 도입을 고민하는 단계가 선행되어야 하는 것일까?

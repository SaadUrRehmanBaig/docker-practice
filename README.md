### Building a Microservices Architecture with Docker Compose

Are you setting up a microservices architecture and want an isolated yet efficient configuration? Let’s break down a `docker-compose.yml` file that demonstrates a clean, scalable setup for a frontend, backend APIs, and databases.

#### Key Highlights:

- Services:
  - `mongo` (MongoDB for Node.js backend).
  - `sql` (MySQL for Java backend).
  - `node_api` (Node.js service).
  - `java_api` (Java-based backend service).
  - `angular` (Frontend application).
- Networks:
  - `FE-network` (Frontend communication with backend services).
  - `BE-node-network` (Node.js backend communication with MongoDB).
  - `BE-java-network` (Java backend communication with MySQL).
- Volumes:
  - `mongo-data` (Persistent data for MongoDB).
  - `sql-data` (Persistent data for MySQL).

Here’s the full `docker-compose.yml` file:

```yaml
version: "3.8"
services:
  mongo:
    container_name: "mongo-db"
    image: mongo:4
    volumes:
      - mongo-data:/data/db
    networks:
      - BE-node-network

  sql:
    container_name: "sql-db"
    image: mysql:8.0.33
    volumes:
      - sql-data:/var/lib/mysql
    networks:
      - BE-java-network
    environment:
      - MYSQL_ROOT_PASSWORD=emartdbpass
      - MYSQL_DATABASE=books

  node_api:
    container_name: "node_api"
    build:
      context: "./nodeapi"
    networks:
      - FE-network
      - BE-node-network
    depends_on:
      - mongo

  java_api:
    container_name: "java_api"
    build:
      context: "./javaapi"
    networks:
      - FE-network
      - BE-java-network
    depends_on:
      - sql

  angular:
    container_name: "client"
    build:
      context: "./client"
    networks:
      - FE-network
    ports:
      - "4200:4200"
    depends_on:
      - java_api
      - node_api

volumes:
  mongo-data:
  sql-data:

networks:
  FE-network:
  BE-node-network:
  BE-java-network:
```

---

#### How It Works:

- **Isolated Networks:**

  - `mongo` communicates only with `node_api` via `BE-node-network`.
  - `sql` communicates only with `java_api` via `BE-java-network`.
  - `angular` interacts with both `node_api` and `java_api` via `FE-network`.

- **Persistent Data:**

  - MongoDB and MySQL data persist using Docker volumes (`mongo-data`, `sql-data`).

- **Dependencies:**

  - `depends_on` ensures services like databases start before their dependent applications.

---

### Benefits of This Setup:

1. **Scalability:** Each service is isolated and can scale independently.
2. **Security:** Services are only accessible on their respective networks.
3. **Portability:** Simplified deployment across environments.

---

#### Ready to Deploy?

Run the following command:

```bash
docker-compose up -d
```

This command builds and starts all services in detached mode. Your Angular app will be accessible at `http://localhost:4200`.

---

Got questions or suggestions? Let me know in the comments! 🚀


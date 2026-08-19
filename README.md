# Blog Engine REST API

A production-ready, feature-rich **Blog REST API** built with **Java 21**, **Spring Boot 3**, **Spring Security**, **JWT Authentication**, and **MySQL/H2 Data Persistence**. Integrated with **Springdoc OpenAPI 3 / Swagger UI** for interactive API testing and containerized for seamless cloud deployment on platforms like **Render** and **Docker**.

---

## 🚀 Features

- **User Authentication & Authorization**: Secure User Registration and Login returning signed JWT Bearer tokens.
- **Blog Post Management**: Full CRUD operations (Create, Read, Update, Delete) for posts with owner & admin authorization checks.
- **Post Search & Filtering**: Search posts by title keyword or retrieve posts by author ID.
- **Commenting Engine**: Add comments to specific posts, view comments per post, and delete comments with owner permissions.
- **Interactive Swagger UI**: Full OpenAPI 3 documentation with built-in JWT Bearer token authentication support.
- **Zero-Cost Cloud Deployment Ready**: Prepared for 1-click deployment on Render free tier (with H2 embedded or external MySQL database).
- **Dockerized**: Includes multi-stage `Dockerfile` and `docker-compose.yml` for database orchestration.

---

## 🛠️ Tech Stack

| Component | Technology |
| :--- | :--- |
| **Language** | Java 21 |
| **Framework** | Spring Boot 3.5.x |
| **Security** | Spring Security 6, JJWT 0.12.5 (JSON Web Tokens) |
| **Persistence** | Spring Data JPA, Hibernate, MySQL 8 / H2 |
| **API Documentation** | Springdoc OpenAPI 3, Swagger UI |
| **Build & Container** | Maven 3.9+, Docker, Docker Compose |

---

## 📂 Project Architecture

```
src/main/java/com/example/blog/
├── config/              # Security and OpenAPI 3 / Swagger configurations
├── controller/          # REST Endpoints (Auth, Post, Comment)
├── dto/                 # Request/Response payloads
├── entity/              # JPA Entities (User, Post, Comment)
├── enums/               # Enums (Role)
├── exception/           # Global Exception Handling & Custom Exceptions
├── repository/          # Spring Data JPA Repositories
├── security/            # JWT Filter and Token Utility
└── service/             # Business Logic & Service Interfaces
```

---

## 📖 API Documentation & Swagger UI

Once the application is running, open your browser to access the interactive **Swagger UI**:

👉 **`http://localhost:8080/swagger-ui.html`** (or `http://localhost:8080/swagger-ui/index.html`)

### 🔐 How to Authorize Requests in Swagger UI:
1. Go to the **Authentication** section and execute `POST /auth/login` (or `/auth/register` first).
2. Copy the generated `token` string from the JSON response.
3. Click the **Authorize 🔓** button at the top right of the Swagger UI page.
4. Paste your token into the **Value** box and click **Authorize**.
5. All subsequent requests to protected endpoints (`/posts`, `/comments`) will automatically attach your Bearer token!

---

## ⚙️ Environment Variables

The application reads configuration from `application.properties` with fallback defaults. You can customize them via environment variables:

| Variable Name | Default Value | Description |
| :--- | :--- | :--- |
| `PORT` | `8080` | Web server port |
| `SPRING_DATASOURCE_URL` | `jdbc:mysql://localhost:3306/blog_db...` | Database JDBC URL |
| `SPRING_DATASOURCE_USERNAME` | `root` | Database username |
| `SPRING_DATASOURCE_PASSWORD` | `9431` | Database password |
| `JWT_SECRET` | `yourVerySecretKey...` | Secret key for JWT signing |
| `SPRING_PROFILES_ACTIVE` | `default` | Set `render` to use embedded H2 DB |

---

## 💻 Local Setup & Execution

### Prerequisites
- JDK 21+
- Apache Maven 3.8+
- MySQL Server 8.0+ (running locally on port 3306)

### 1. Configure MySQL Database
Create a local MySQL database named `blog_db` (or ensure your MySQL server is running):
```sql
CREATE DATABASE blog_db;
```

### 2. Build & Run
```bash
# Clone or navigate to the project folder
cd blog-rewrite

# Compile and package
mvn clean package -DskipTests

# Run Spring Boot
mvn spring-boot:run
```

---

## 🐳 Docker Deployment

### Run Application & MySQL with Docker Compose
To launch both the MySQL 8 database container and the Spring Boot application together:

```bash
docker-compose up --build -d
```
- Access API at `http://localhost:8080`
- Access Swagger UI at `http://localhost:8080/swagger-ui.html`
- Stop containers: `docker-compose down`

---

## 🌐 Render Free Tier Deployment Guide

Render allows you to host web services for free. You have **two choices** to deploy this app to Render for $0:

### 🌟 Option 1: Standalone Single Web Service (Zero External DB Setup)
Use the built-in **`render`** profile which runs an embedded H2 database in memory. This requires **only 1 free Render Web Service**:

1. Push your repository to **GitHub** or **GitLab**.
2. Log into [Render Dashboard](https://dashboard.render.com/) and click **New +** -> **Web Service**.
3. Connect your repository.
4. Set the following settings:
   - **Environment**: `Docker` (Render automatically detects the included `Dockerfile`).
   - **Region**: Choose nearest region.
   - **Instance Type**: `Free`.
5. Add Environment Variable:
   - Key: `SPRING_PROFILES_ACTIVE`
   - Value: `render`
6. Click **Create Web Service**. Your API and Swagger UI will be live online!

---

### Option 2: Render Web Service + Free External Cloud MySQL
If you want persistent MySQL storage without paying Render:

1. Create a free MySQL database on a cloud database provider like [Aiven for MySQL](https://aiven.io/) or [Clever Cloud](https://www.clever-cloud.com/).
2. On Render Web Service, add these Environment Variables:
   - `SPRING_DATASOURCE_URL`: `jdbc:mysql://<your-aiven-host>:<port>/<dbname>?useSSL=true`
   - `SPRING_DATASOURCE_USERNAME`: `<your-db-username>`
   - `SPRING_DATASOURCE_PASSWORD`: `<your-db-password>`
   - `JWT_SECRET`: `<a-long-random-secret-key>`
3. Deploy!

---

## 📋 API Reference Summary

### Authentication (`/auth`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/auth/register` | Public | Register new user account |
| `POST` | `/auth/login` | Public | Authenticate user & get JWT token |

### Posts (`/posts`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/posts` | Authenticated | Create a new blog post |
| `GET` | `/posts` | Authenticated | Get list of all blog posts |
| `GET` | `/posts/{id}` | Authenticated | Get post details by ID |
| `PUT` | `/posts/{id}` | Authenticated (Owner) | Update an existing post |
| `DELETE`| `/posts/{id}` | Authenticated (Owner/Admin) | Delete a post |
| `GET` | `/posts/search?title=...` | Authenticated | Search posts by title |
| `GET` | `/posts/user/{userId}` | Authenticated | Get all posts by author ID |

### Comments (`/posts/{postId}/comments`)
| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/posts/{postId}/comments` | Authenticated | Add a comment to a post |
| `GET` | `/posts/{postId}/comments` | Authenticated | Get all comments for a post |
| `DELETE`| `/posts/comments/{id}` | Authenticated (Owner/Admin) | Delete a comment by ID |

---

## 📄 License
This project is licensed under the MIT License.

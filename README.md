# URL Shortener

A production-ready Spring Boot microservice for shortening URLs. Built with clean layered architecture, deployed on Render.

**🔗 Live Demo:** https://url-shortner-l0q1.onrender.com

> Free tier note: the service sleeps after 15 minutes of inactivity. The first request after sleeping takes 30–60 seconds to wake up.

---

## Features

- **Shorten URLs** — Paste a long URL, get a short 6-character code
- **Idempotent** — Same long URL always returns the same short link
- **Collision-resistant** — Base62 codes via `SecureRandom` with retry guard
- **Auto-redirect** — `GET /{shortCode}` → 302 to original URL
- **Input validation** — Rejects empty/invalid URLs with clear errors
- **Responsive UI** — Single-page Thymeleaf template with copy-to-clipboard
- **Cloud-ready** — Environment-variable DB config for local MySQL or cloud PostgreSQL

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Language | Java 17 |
| Framework | Spring Boot 3.4.5 |
| Data | Spring Data JPA, Hibernate |
| Database | MySQL 8 (local), PostgreSQL (cloud) |
| Frontend | Thymeleaf, vanilla HTML/CSS/JS |
| Build | Gradle 8.4 |
| Deploy | Render (free tier) |

---

## Quick Start (Local)

### Prerequisites
- Java 17+
- MySQL 8 running locally
- Database `urlshortener` created (`CREATE DATABASE urlshortener;`)

### Run
```bash
# Clone
git clone https://github.com/Pankajmonde/Url-Shortner.git
cd Url-Shortner

# Run (uses local MySQL defaults: root/root)
./gradlew bootRun
```

App starts at **http://localhost:8081**

---

## Configuration

All DB settings are environment-variable driven with local MySQL defaults:

| Env Var | Default | Description |
|---------|---------|-------------|
| `DB_URL` | `jdbc:mysql://localhost:3306/urlshortener` | JDBC URL |
| `DB_USERNAME` | `root` | Database username |
| `DB_PASSWORD` | `root` | Database password |
| `DB_DRIVER` | `com.mysql.cj.jdbc.Driver` | JDBC driver class |
| `DB_DIALECT` | `org.hibernate.dialect.MySQLDialect` | Hibernate dialect |

**For Render (PostgreSQL):**
```bash
DB_URL=jdbc:postgresql://<host>:5432/<db>
DB_USERNAME=<user>
DB_PASSWORD=<pass>
DB_DRIVER=org.postgresql.Driver
DB_DIALECT=org.hibernate.dialect.PostgreSQLDialect
```

---

## Project Structure

```
src/main/java/com/example/URLShortenerService/
├── UrlShortenerServiceApplication.java   # Spring Boot entry point
├── controller/
│   └── HomeController.java               # GET /, POST /shorten, GET /{shortCode}
├── entity/
│   └── ShortUrl.java                     # JPA entity (short_urls table)
├── model/
│   ├── ShortenUrlRequest.java            # DTO: { url }
│   └── ShortenUrlResponse.java           # DTO: shortCode, shortUrl, originalUrl, timestamps
├── repository/
│   └── ShortUrlRepository.java           # JPA repo with findByShortCode / findByOriginalUrl
├── service/
│   └── UrlShortenerService.java          # Business logic: createShortUrl, getOriginalUrl
└── util/
    └── ShortCodeGenerator.java           # SecureRandom Base62 generator (6 chars)
```

---

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/` | Show shorten form |
| `POST` | `/shorten` | Submit URL, return short link |
| `GET` | `/{shortCode:[a-zA-Z0-9]{6}}` | Redirect to original URL |

---

## Deployment (Render)

1. Push to GitHub
2. Create **Web Service** on Render → connect repo
3. Build Command: `./gradlew build -x test`
4. Start Command: `java -jar build/libs/*.jar`
5. Add environment variables for PostgreSQL (see table above)
6. Deploy → get `https://your-app.onrender.com`

---

## License

MIT — free to use, modify, and distribute.
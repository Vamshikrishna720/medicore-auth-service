# medicore-auth-service

Identity & access service for the **MediCore** healthcare platform
([monorepo](https://github.com/Vamshikrishna720/medicore)): user registration,
BCrypt login, **JWT issuing**, and account **deactivation as soft delete**.

## Highlights

- **Spring Security** stateless chain + **jjwt** (HS256) token issuing — claims: `userId`, `email`, `role` (ADMIN/DOCTOR/PATIENT)
- **Deactivation, not deletion** — `active=false` + `deactivatedAt`; reversible, records retained (healthcare compliance thinking). Deactivated users can't log in; ADMIN can restore anyone.
- ADMIN user management: paginated search, activate/deactivate any account
- Seeded demo accounts (first boot): `admin@medicore.com/Admin@123`, `doctor@medicore.com/Doctor@123`, `patient@medicore.com/Patient@123`

## Endpoints (via gateway, `/api`)

| Method | Path | Access |
|---|---|---|
| POST | `/auth/register` (PATIENT/DOCTOR) | public |
| POST | `/auth/login` → JWT | public |
| GET | `/auth/me` | JWT |
| DELETE | `/auth/me` | JWT (self-deactivate) |
| GET | `/auth/users?search=&page=&size=` | ADMIN |
| PATCH | `/auth/users/{id}/status` `{active}` | ADMIN |
| GET | `/internal/users/{id}` | internal token |

## Run

> **Prerequisite:** this repo depends on `com.medicore:medicore-common:1.0.0`. Install it to your local Maven repo first — clone [medicore-common](https://github.com/Vamshikrishna720/medicore-common) and run `mvn clean install` there. CI has the same requirement (publishing common to GitHub Packages would make this repo fully self-contained).

```bash
mvn spring-boot:run          # :8081 (needs MySQL + Eureka, see application.yml)
```

| Env var | Default | Purpose |
|---|---|---|
| `DB_HOST` / `DB_USER` / `DB_PASSWORD` | localhost / medicore / medicore123 | MySQL `medicore_auth` |
| `EUREKA_URI` | http://localhost:8761/eureka | Registry |
| `JWT_SECRET` | dev value | Must match gateway & services |
| `INTERNAL_TOKEN` | dev value | Guards `/internal/**` |

Swagger: `http://localhost:8081/swagger-ui/index.html`

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**苍穹外卖 (Sky Takeout)** - A Spring Boot-based food delivery platform with admin management and WeChat mini-program user-facing features.

## Build & Run Commands

```bash
# Build entire project
mvn clean package -DskipTests

# Run the application (from sky-server module)
mvn spring-boot:run -pl sky-server

# Run with Maven wrapper (if available)
./mvnw spring-boot:run -pl sky-server

# Run tests
mvn test

# Run tests for a specific module
mvn test -pl sky-server
```

The app starts on port **8080**. API docs (Knife4j/Swagger UI) are available at `http://localhost:8080/doc.html`.

## Module Architecture

This is a Maven multi-module project:

- **sky-common** — Shared utilities, constants, exceptions, JWT utils, result wrappers, ThreadLocal context (`BaseContext`), Aliyun OSS util, WeChat Pay util
- **sky-pojo** — Data transfer objects (DTOs), entities, view objects (VOs); no business logic
- **sky-server** — Main Spring Boot app: controllers, services, mappers, interceptors, config

## Key Architectural Patterns

### Layered Structure (sky-server)
```
controller/admin/   — Admin REST endpoints (prefixed /admin/**)
controller/user/    — User-facing endpoints (for WeChat mini-program)
service/            — Service interfaces
service/impl/       — Service implementations
mapper/             — MyBatis mapper interfaces
interceptor/        — JWT interceptors
config/             — Spring MVC and bean configurations
handler/            — Global exception handler
```

### Authentication
- JWT-based auth via `JwtTokenAdminInterceptor` applied to all `/admin/**` routes except `/admin/employee/login`
- Token is passed in request header `token`
- Current user ID is stored per-request in `BaseContext` (ThreadLocal), set by the interceptor after token validation

### Response Format
All API responses use `Result<T>` wrapper from `sky-common`:
- `Result.success(data)` for successful responses
- `Result.error(message)` for error responses
- Business exceptions extend `BaseException` and are caught by `GlobalExceptionHandler`

### Data Layer
- MyBatis with both annotation-based SQL (in mapper interfaces) and XML mapper files (`sky-server/src/main/resources/mapper/`)
- Underscore-to-camelCase mapping is enabled globally
- Druid connection pool

### Configuration
- Active profile: `dev` (set in `application.yml`)
- Environment-specific config: `application-dev.yml`
- Database: MySQL on `localhost:3306`, database `sky_take_out`, user `root`
- JWT secret key and TTL configured under `sky.jwt.*`
- Aliyun OSS credentials under `sky.alioss.*`
- WeChat properties under `sky.wechat.*`

## Conventions

- All constants go in `sky-common/...constant/` (e.g., `MessageConstant`, `StatusConstant`, `PasswordConstant`)
- All custom exceptions extend `BaseException` in `sky-common/...exception/`
- Use `@Slf4j` + `log.info()`/`log.error()` for logging
- Entities use Lombok (`@Data`, `@Builder`, `@NoArgsConstructor`, `@AllArgsConstructor`)
- Default employee password is MD5 of `PasswordConstant.DEFAULT_PASSWORD` on creation
- `BaseContext.getCurrentId()` retrieves the logged-in employee/user ID within a request

# ARCTIC — Automated Range Creation Tool and Integrated Console

ARCTIC is a hypervisor-agnostic cyber range orchestration platform. The idea is simple — you define your networks, hosts, volumes, and security groups as generic objects, and ARCTIC figures out how to build them on whatever hypervisor you're targeting. No deep Terraform or Ansible expertise needed.

Everything runs as a Spring Boot 3 multi-module Maven monolith on Java 17.

---

## Modules

| Module | What it does |
|--------|-------------|
| **Flock** | Spring Boot entry point. Every single REST endpoint lives here. |
| **Orca** | JWT auth and Spring Security. Handles users, roles, and login. |
| **Iceberg** | Range data model (hosts, networks, volumes, etc.) and the build scheduler. |
| **Shard** | Plugin loader and hypervisor connector framework. Loads JARs at runtime from `providers/`. |
| **Polarbear** | Attack step and scenario definitions for exercises. |

---

## How it Works

1. You create an exercise in Iceberg — networks, hosts, routers, security groups, the whole thing.
2. You configure a Shard profile pointing at your hypervisor (OpenStack, etc.).
3. You hit the build endpoint and Iceberg's scheduler kicks off, handing tasks to Shard in priority order.
4. Shard calls your hypervisor's API via the loaded plugin and provisions everything.

---

## API Prefixes

- `/range-api/v1/**` — ranges, exercises, hosts, networks, routers, profiles, users
- `/polarbear-api/v1/**` — attack steps
- `/range-api/v1/admin/**` — admin operations (provider management, etc.)

---

## Running It

Standard Spring Boot with Maven. You need a MySQL database configured. Drop any provider plugin JARs into a `providers/` folder next to the JAR and they'll load on startup.

```
mvn spring-boot:run
```

The frontend (Vite + React) runs separately on `localhost:5173`.

---

## Tech Stack

- Java 17, Spring Boot 3.2.2
- Maven multi-module
- JPA/Hibernate + MySQL
- Stateless JWT auth
- Custom async task system (`ArcticTask`)
- Runtime JAR loading via `URLClassLoader`

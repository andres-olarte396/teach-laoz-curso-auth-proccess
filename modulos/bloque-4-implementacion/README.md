# Bloque 4 — Implementación Práctica (Paso a Paso)

## Objetivos del Bloque
Implementar un sistema completo de autenticación y autorización con .NET 8, Keycloak, y componentes de infraestructura, desde cero hasta un entorno funcional con validación de tokens, políticas y auditoría.

## Duración estimada
12-16 horas (hands-on intensivo)

---

## Stack Tecnológico

### Componentes Principales
- **Backend**: .NET 8 (ASP.NET Core)
- **Identity Provider**: Keycloak (self-hosted) o Auth0/Okta (SaaS)
- **API Gateway**: YARP (Yet Another Reverse Proxy - .NET)
- **Policy Engine**: OPA (Open Policy Agent) con Rego
- **Base de datos**: PostgreSQL
- **Contenedores**: Docker + Docker Compose
- **Observabilidad**: Prometheus + Grafana + Seq (logs)

### Alternativas (para otros stacks)
- Node.js: Express/Koa + Passport + oidc-client
- Java: Spring Boot + Spring Security OAuth2
- Python: FastAPI + python-jose

---

## Guía de Implementación Paso a Paso

### [Paso 1: Levantar Identity Provider](./paso-1-setup-idp.md)
**Duración**: 1-2 horas

- Instalar Keycloak con Docker Compose
- Crear realm y configuración inicial
- Crear clients (SPA, API Gateway, Servicios)
- Configurar roles y scopes
- Configurar mappers para claims
- Publicar JWKS endpoint

**Entregables:**
- `docker-compose.yml` con Keycloak
- Realm export JSON
- Documentación de configuración

---

### [Paso 2: Configurar Clientes y Flujos OAuth2](./paso-2-oauth-flows.md)
**Duración**: 2-3 horas

- Configurar cliente para Authorization Code + PKCE (SPA)
- Configurar cliente para Client Credentials (M2M)
- Mapear roles a tokens
- Configurar scopes personalizados
- Probar flujos con Postman/curl

**Entregables:**
- Colección Postman con flujos completos
- Scripts de prueba
- Documentación de endpoints

---

### [Paso 3: Implementar API Gateway con YARP](./paso-3-api-gateway.md)
**Duración**: 2-3 horas

- Crear proyecto ASP.NET Core para Gateway
- Configurar YARP routing
- Implementar middleware de validación JWT
- Validar firma, iss, aud, exp
- Consumir JWKS y cache
- Extraer claims y pasar headers a servicios
- Implementar rate limiting

**Entregables:**
- Proyecto Gateway funcional
- Configuración YARP
- Middleware de autenticación
- Tests unitarios

---

### [Paso 4: Implementar Microservicios con Autorización](./paso-4-microservicios.md)
**Duración**: 3-4 horas

- Crear 2-3 microservicios de ejemplo
- Implementar validación de tokens en cada servicio
- Autorización basada en roles/scopes
- Autorización fina (resource-level)
- Integración con OPA para políticas complejas

**Servicios de ejemplo:**
- **Users Service**: CRUD de usuarios (solo admins)
- **Orders Service**: crear/ver pedidos (solo owners o admins)
- **Reports Service**: generar reportes (requiere scope específico)

**Entregables:**
- 3 proyectos .NET de microservicios
- Políticas OPA en Rego
- Tests de autorización

---

### [Paso 5: Refresh Tokens y Revocación](./paso-5-refresh-revocation.md)
**Duración**: 2 horas

- Implementar refresh token flow
- Almacenamiento seguro (httpOnly cookies)
- Endpoint de revocación
- Logout y cleanup
- Manejo de refresh token rotation

**Entregables:**
- Endpoints de refresh y revoke
- Documentación de flujo de logout
- Tests de escenarios de revocación

---

### [Paso 6: Auditoría y Logging](./paso-6-auditoria.md)
**Duración**: 2 horas

- Centralizar logs con Seq
- Structured logging
- Eventos críticos:
  - Login success/failure
  - Token refresh/revocation
  - Authorization denials
  - Anomalías
- Correlación con trace-id (OpenTelemetry)
- Dashboard básico en Grafana

**Entregables:**
- Configuración de logging estructurado
- Dashboard Grafana
- Alertas básicas

---

### [Paso 7: Operaciones y Seguridad](./paso-7-operaciones.md)
**Duración**: 2 horas

- Rotación de claves (simulación)
- Monitoreo de métricas auth
- Health checks
- Backup y recovery del IdP
- Checklist de hardening

**Entregables:**
- Scripts de rotación de claves
- Configuración de health checks
- Documentación operacional

---

### [Paso 8: Testing de Seguridad](./paso-8-security-testing.md)
**Duración**: 2-3 horas

- Unit tests de validación de tokens
- Integration tests de flujos OAuth2
- Tests de autorización
- Pentesting básico:
  - Token tampering
  - Token replay
  - Expired token handling
  - CSRF protection
  - Rate limiting bypass attempts

**Entregables:**
- Suite de tests completa
- Reporte de vulnerabilidades encontradas
- Fixes aplicados

---

## Arquitectura Final

```
┌──────────────┐
│     SPA      │
│  (React/Vue) │
└──────┬───────┘
       │
       │ 1. Login (Auth Code + PKCE)
       ▼
┌─────────────────┐
│   Keycloak      │
│  (IdP)          │
│  - Realm        │
│  - Clients      │
│  - JWKS         │
└────────┬────────┘
         │ 2. Tokens (JWT)
         ▼
┌─────────────────────────────────┐
│      API Gateway (YARP)         │
│  - Validar JWT (RS256)          │
│  - Verificar iss, aud, exp      │
│  - Rate limiting                │
│  - Extraer claims → headers     │
└─────┬───────────────────────────┘
      │
      ├──────► ┌──────────────────┐      ┌─────────┐
      │        │  Users Service   │─────►│   OPA   │
      │        │  [Authorize]     │      │ (Rego)  │
      │        └──────────────────┘      └─────────┘
      │
      ├──────► ┌──────────────────┐
      │        │  Orders Service  │
      │        │  [Authorize]     │
      │        └──────────────────┘
      │
      └──────► ┌──────────────────┐
               │  Reports Service │
               │  [Authorize]     │
               └──────────────────┘
                       │
                       ▼
               ┌──────────────────┐
               │  Audit & Logs    │
               │  (Seq + Grafana) │
               └──────────────────┘
```

---

## Docker Compose Completo

Al final del bloque tendrás un `docker-compose.yml` con:
- Keycloak
- PostgreSQL (para Keycloak)
- Gateway (.NET)
- 3 Microservicios (.NET)
- OPA
- Seq (logs)
- Prometheus
- Grafana

Todo orquestado y funcional con un solo comando:
```bash
docker-compose up -d
```

---

## Checklist de Implementación

- [ ] Keycloak corriendo con realm configurado
- [ ] Authorization Code + PKCE funcionando desde SPA
- [ ] Client Credentials funcionando para M2M
- [ ] Gateway validando tokens correctamente
- [ ] 3 microservicios con autorización implementada
- [ ] OPA integrado con al menos 1 política ABAC
- [ ] Refresh tokens funcionando
- [ ] Endpoint de revocación operativo
- [ ] Logs estructurados en Seq
- [ ] Dashboard Grafana con métricas auth
- [ ] Suite de tests pasando (>80% coverage en auth logic)
- [ ] Rotación de claves documentada y probada

---

## Recursos del Bloque

- 📦 [Código fuente completo](./codigo-fuente/)
- 📄 [Scripts de Docker Compose](./docker/)
- 📄 [Colección Postman](./postman/)
- 📄 [Políticas OPA](./opa-policies/)
- 📊 [Dashboards Grafana](./grafana/)

---

## Evaluación

Al completar este bloque deberías:
- [ ] Tener un sistema completo funcionando end-to-end
- [ ] Poder explicar cada componente y su responsabilidad
- [ ] Poder troubleshootear problemas de autenticación/autorización
- [ ] Entender el flujo completo de un request autenticado
- [ ] Haber implementado al menos 5 tipos diferentes de políticas de autorización
- [ ] Poder deployar el sistema completo con Docker Compose

---

[⬅️ Bloque anterior: Diseño](../bloque-3-diseno-microservicios/README.md) | [Volver al índice principal](../../README.md) | [Siguiente: Bloque 5 - Políticas Avanzadas ➡️](../bloque-5-politicas-avanzadas/README.md)

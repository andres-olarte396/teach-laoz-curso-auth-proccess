# Bloque 5 — Políticas Avanzadas y Escenarios Reales

## Objetivos del Bloque

Dominar modelos avanzados de autorización (ABAC, UMA), implementar flujos complejos como token exchange y on-behalf-of, y resolver casos de uso reales en sistemas distribuidos.

## Duración estimada

6-8 horas

---

## Contenido

### 1. [Autorización Basada en Atributos (ABAC) Avanzada](./01-abac-avanzado.md)

- Atributos de usuario (departamento, nivel, antigüedad)
- Atributos de recurso (estado, confidencialidad, owner)
- Atributos de contexto (hora, geolocalización, dispositivo)
- Combinación de atributos en políticas complejas
- Performance considerations

### 2. [User-Managed Access (UMA) 2.0](./02-uma.md)

- Qué es UMA y casos de uso
- Delegated authorization
- Consent screens
- Permission tickets
- Claims gathering flow

### 3. [Policy Decision Point (PDP) Patterns](./03-pdp-patterns.md)

- Embedded PDP vs Remote PDP
- Policy Information Point (PIP)
- Policy Administration Point (PAP)
- OPA como PDP centralizado
- Caching de decisiones

### 4. [OAuth2 Token Exchange (RFC 8693)](./04-token-exchange.md)

- Qué problema resuelve
- Token exchange flow
- Impersonation vs Delegation
- Casos de uso: microservicio actuando en nombre del usuario

### 5. [On-Behalf-Of Flow](./05-on-behalf-of.md)

- Escenario: Service A llama Service B en nombre del usuario
- Implementación en Azure AD
- Implementación en Keycloak
- Propagación de identidad

### 6. [Políticas Contextuales](./06-politicas-contextuales.md)

- Geolocalización
- Horario (business hours)
- Tipo de dispositivo (mobile/desktop)
- Red (interna/externa)
- Nivel de riesgo dinámico

### 7. [Fine-Grained Authorization](./07-fine-grained-authz.md)

- Object-level authorization
- Field-level authorization (GraphQL)
- Row-level security (RLS) en BD
- Filter-based policies

---

## Casos de Uso Reales

### Caso 1. Sistema de Salud (HIPAA Compliance)

**Requisitos:**

- Solo médicos del departamento pueden ver historiales de SUS pacientes
- Enfermeras solo pueden ver datos básicos, no diagnósticos
- Acceso solo desde red interna del hospital
- Auditoría completa de accesos

**Política ABAC:**

```rego
# OPA Policy
allow {
    input.user.role == "doctor"
    input.user.department == input.resource.department
    input.patient.id in input.user.assigned_patients
    is_internal_network(input.context.ip)
}

allow {
    input.user.role == "nurse"
    input.resource.type == "basic_info"
    input.user.department == input.resource.department
    is_business_hours(input.context.time)
}
```

---

### Caso 2: Plataforma Multi-Tenant SaaS

**Requisitos:**

- Usuarios de Tenant A no pueden ver datos de Tenant B
- Admin global puede ver todos los tenants
- Límites por tier (free/pro/enterprise)
- Feature flags por tenant

**Implementación:**

```csharp
[Authorize]
[TenantIsolation] // Custom attribute
public IActionResult GetProjects()
{
    var tenantId = User.FindFirst("tenant_id").Value;
    var tier = User.FindFirst("tenant_tier").Value;

    var projects = _db.Projects
        .Where(p => p.TenantId == tenantId)
        .Take(GetProjectLimit(tier));

    return Ok(projects);
}
```

---

### Caso 3: Sistema Bancario - Transferencias

**Requisitos:**

- Solo puedes transferir desde TUS cuentas
- Transferencias >$10,000 requieren aprobación adicional
- Transferencias internacionales solo en horario bancario
- Detección de anomalías (ubicación inusual)

**Política compleja:**

```rego
allow_transfer {
    owns_source_account
    within_daily_limit
    not is_suspicious_location
    not requires_additional_approval
}

allow_transfer {
    owns_source_account
    requires_additional_approval
    has_approval_from_manager
}

owns_source_account {
    input.user.id == input.source_account.owner_id
}

requires_additional_approval {
    input.amount > 10000
}

is_suspicious_location {
    distance_km(input.user.last_location, input.context.location) > 500
    time_since_last_login_hours < 1
}
```

---

### Caso 4: API Gateway con Rate Limiting Dinámico

**Requisitos:**

- Free tier: 100 req/hour
- Pro tier: 1000 req/hour
- Enterprise: ilimitado
- Rate limiting por API key + user

**Implementación:**

```csharp
public class DynamicRateLimitMiddleware
{
    public async Task InvokeAsync(HttpContext context)
    {
        var tier = context.User.FindFirst("subscription_tier")?.Value;
        var limit = tier switch {
            "enterprise" => int.MaxValue,
            "pro" => 1000,
            _ => 100
        };

        var key = $"{context.User.Identity.Name}:{DateTime.UtcNow:yyyyMMddHH}";
        var count = await _cache.IncrementAsync(key);

        if (count > limit)
        {
            context.Response.StatusCode = 429;
            await context.Response.WriteAsync("Rate limit exceeded");
            return;
        }

        await _next(context);
    }
}
```

---

### Caso 5: Long-Running Jobs (Background Tasks)

**Requisitos:**

- Job iniciado por usuario pero corre durante horas
- Job necesita acceder a APIs con credenciales del usuario
- Token del usuario expira antes de que termine el job

**Solución: Client Credentials con Bounded Privilege**

```csharp
// Al crear el job, guardar contexto del usuario
public async Task<string> CreateReportJob(GenerateReportRequest request)
{
    var userId = User.FindFirst(ClaimTypes.NameIdentifier).Value;
    var userScopes = User.FindAll("scope").Select(c => c.Value).ToList();

    var job = new ReportJob {
        UserId = userId,
        AllowedScopes = userScopes, // Privilegios limitados
        Parameters = request,
        Status = "Pending"
    };

    await _db.Jobs.AddAsync(job);
    await _backgroundQueue.EnqueueAsync(job.Id);

    return job.Id;
}

// Background worker usa Client Credentials
public async Task ProcessJob(string jobId)
{
    var job = await _db.Jobs.FindAsync(jobId);

    // Obtener token de servicio (no del usuario)
    var token = await _tokenService.GetClientCredentialsTokenAsync(
        scopes: job.AllowedScopes // Limitado a scopes del usuario original
    );

    // Procesar job con privilegios acotados
    await GenerateReport(job, token);
}
```

---

## Actividades Prácticas

### Lab 1. Implementar ABAC con OPA

📝 [Ver lab completo](./labs/lab-1-abac-opa.md)

Implementar política ABAC para sistema de gestión documental:

- Atributos de usuario: rol, departamento, nivel
- Atributos de documento: clasificación, departamento, estado
- Reglas: solo usuarios del mismo departamento + nivel suficiente

---

### Lab 2: Token Exchange Flow

📝 [Ver lab completo](./labs/lab-2-token-exchange.md)

Implementar flujo donde:

- Usuario autentica en SPA
- SPA llama API A
- API A necesita llamar API B en nombre del usuario
- Usar token exchange para obtener token para API B

---

### Lab 3: Políticas Contextuales

📝 [Ver lab completo](./labs/lab-3-contexto.md)

Implementar políticas que consideren:

- Horario (solo business hours para operaciones críticas)
- Geolocalización (bloquear si fuera del país)
- Dispositivo (requerir MFA si dispositivo nuevo)

---

## Herramientas Avanzadas

### Open Policy Agent (OPA)

```bash
# Instalar OPA
docker run -d --name opa -p 8181:8181 openpolicyagent/opa:latest run --server

# Cargar política
curl -X PUT http://localhost:8181/v1/policies/authz \
  --data-binary @authz.rego

# Evaluar decisión
curl -X POST http://localhost:8181/v1/data/authz/allow \
  -H "Content-Type: application/json" \
  -d @input.json
```

### Casbin

Alternativa a OPA, más ligera:

```csharp
var enforcer = new Enforcer("model.conf", "policy.csv");

if (await enforcer.EnforceAsync(user, resource, action))
{
    // Permitir
}
```

---

## Checklist de Políticas Avanzadas

- [ ] Políticas ABAC implementadas con al menos 3 tipos de atributos
- [ ] PDP centralizado (OPA o similar) integrado
- [ ] Caching de decisiones para performance
- [ ] Token exchange implementado para M2M
- [ ] On-behalf-of flow para propagación de identidad
- [ ] Políticas contextuales (hora/geo/dispositivo)
- [ ] Fine-grained authorization a nivel de objeto
- [ ] Auditoría completa de decisiones de autorización
- [ ] Tests de políticas con múltiples escenarios
- [ ] Documentación de políticas en lenguaje natural

---

## Errores Comunes

❌ **Over-engineering de políticas**

- No todo necesita ABAC, a veces RBAC simple es suficiente
- Evalúa complejidad vs beneficio

❌ **PDP sin cache**

- Llamar al PDP por cada request mata performance
- Cachea decisiones con TTL corto

❌ **Atributos inconsistentes**

- Si el token dice `department=sales` pero la BD dice `marketing`, falla
- Sincroniza fuentes de atributos

❌ **Políticas no versionadas**

- Cambios en políticas pueden romper aplicaciones
- Versiona y testea políticas antes de deploy

---

## Recursos Adicionales

- 📄 [OPA Playground](https://play.openpolicyagent.org/)
- 📄 [OPA Best Practices](./recursos/opa-best-practices.md)
- 📄 [Casbin Editor](https://casbin.org/editor/)
- 📚 [Especificación UMA 2.0](./recursos/uma-spec.md)
- 📚 [RFC 8693 - Token Exchange](./recursos/rfc-8693.md)

---

## Evaluación

Al completar este bloque deberías poder:

- [ ] Diseñar e implementar políticas ABAC complejas
- [ ] Integrar OPA como PDP en arquitectura de microservicios
- [ ] Implementar token exchange y on-behalf-of flows
- [ ] Resolver casos de uso reales con políticas contextuales
- [ ] Evaluar performance de políticas y optimizar
- [ ] Documentar y versionar políticas de autorización

---

[⬅️ Bloque anterior: Implementación](../bloque-4-implementacion/README.md) | [Volver al índice principal](../../README.md)

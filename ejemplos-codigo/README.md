# Ejemplos de Código

Esta carpeta contiene ejemplos de código reutilizables organizados por componente.

## 📁 Estructura

### [`gateway-yarp/`](./gateway-yarp/)
API Gateway con YARP (.NET 8):
- Configuración básica de YARP
- Middleware de validación JWT
- Rate limiting
- Headers forwarding

### [`middleware-jwt/`](./middleware-jwt/)
Middleware de autenticación JWT:
- Validación de firma (RS256, HS256)
- Validación de claims (iss, aud, exp, nbf)
- Consumo de JWKS endpoint
- Cache de claves públicas

### [`opa-policies/`](./opa-policies/)
Políticas Open Policy Agent (Rego):
- Políticas RBAC
- Políticas ABAC
- Ejemplos contextuales (tiempo, geo, etc.)
- Tests de políticas

### [`microservices/`](./microservices/)
Ejemplos de microservicios .NET 8:
- Users Service (CRUD con autorización)
- Orders Service (resource-level authz)
- Reports Service (scope-based authz)

---

## 🚀 Uso

Cada carpeta contiene:
- README con instrucciones específicas
- Código fuente completo
- Archivo `.sln` o `package.json`
- Dockerfile (si aplica)
- Tests unitarios

---

[⬅️ Volver al índice principal](../README.md)

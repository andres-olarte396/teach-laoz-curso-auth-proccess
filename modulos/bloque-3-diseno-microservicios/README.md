# Bloque 3 — Diseño de Autenticación en Microservicios

## Objetivos del Bloque

Decidir dónde validar tokens, dónde aplicar políticas de autorización, y cómo distribuir responsabilidades de seguridad en una arquitectura de microservicios.

## Duración estimada

6-8 horas

---

## Contenido

### 1. [Arquitectura de Seguridad en Microservicios](./01-arquitectura-seguridad.md)

- Desafíos de seguridad distribuida
- Principios de Zero Trust
- Defensa en profundidad

### 2. [Componentes Clave](./02-componentes-clave.md)

- **Identity Provider (IdP)** / Authorization Server
- **API Gateway** / Edge Proxy
- **Service Mesh** (Istio, Linkerd)
- **Policy Engine** (OPA, Custom PDP)
- **Audit & Logging Service**
- **Key Management Service**

### 3. [API Gateway - Validación Primaria](./03-api-gateway.md)

- Responsabilidades del gateway
- Validación de tokens JWT
- Rate limiting y throttling
- Enriquecimiento de headers
- ¿Qué NO debe hacer el gateway?

### 4. [Autorización a Nivel de Servicio](./04-autorizacion-servicio.md)

- ¿Por qué los servicios deben validar?
- Autorización basada en claims/scopes
- Autorización fina (resource-level)
- Integración con Policy Engines

### 5. [Patrones de Diseño](./05-patrones-diseno.md)

- **Token Relay**: pasar token a través de servicios
- **Token Exchange**: OAuth2 Token Exchange (RFC 8693)
- **Backend for Frontend (BFF)**: patrón para SPAs
- **Sidecar Pattern**: service mesh para mTLS
- **Claims Enrichment**: agregar contexto al token

### 6. [Gestión de Claves (JWKS)](./06-gestion-claves.md)

- JWKS endpoint
- Rotación de claves
- Caching de claves públicas
- Estrategias de rollover

### 7. [Auditoría y Observabilidad](./07-auditoria-observabilidad.md)

- Eventos críticos a registrar
- Correlación de requests (trace-id)
- Métricas de autenticación/autorización
- Alertas y anomalías

---

## Arquitectura de Referencia

```
┌─────────────┐
│   Cliente   │
│  (SPA/App)  │
└──────┬──────┘
       │ 1. Login
       ▼
┌─────────────────────┐
│  Identity Provider  │◄────── JWKS (claves públicas)
│   (Keycloak/Auth0)  │
└──────┬──────────────┘
       │ 2. Tokens (JWT)
       ▼
┌─────────────────────┐
│    API Gateway      │
│   - Validar token   │
│   - Rate limiting   │
│   - Routing         │
└──────┬──────────────┘
       │ 3. Request + Claims
       ▼
┌─────────────────────┐      ┌──────────────┐
│   Servicio A        │─────►│ Policy Engine│
│ - Validación fina   │      │    (OPA)     │
│ - Business logic    │      └──────────────┘
└──────┬──────────────┘
       │
       ▼
┌─────────────────────┐
│  Audit & Logging    │
│  - Eventos AuthN/Z  │
│  - Trace correlation│
└─────────────────────┘
```

---

## Patrones de Implementación

### Patrón 1. Gateway + Servicios (validación dual)

```
✅ Gateway: Valida token (firma, exp, iss, aud)
✅ Servicios: Validan scopes/roles + autorización fina
```

**Ventajas:** Defensa en profundidad, servicios no confían ciegamente
**Desventajas:** Más latencia (doble validación)

### Patrón 2: Gateway único punto de validación

```
✅ Gateway: Validación completa
❌ Servicios: Confían en gateway, solo leen headers
```

**Ventajas:** Menos latencia, simplificado
**Desventajas:** Si gateway falla, todos fallan; menos seguro

### Patrón 3: Service Mesh (mTLS)

```
✅ Istio/Linkerd: mTLS entre servicios
✅ Gateway: Valida token usuario
✅ Servicios: Validación adicional + policies
```

**Ventajas:** Zero Trust, tráfico encriptado, identidad por servicio
**Desventajas:** Complejidad operacional

---

## Actividades Prácticas

### Ejercicio 1. Diseñar Arquitectura

📝 [Ver ejercicio](./ejercicios/ejercicio-1-arquitectura.md)

Diseña la arquitectura de seguridad para un sistema con:

- 1 SPA frontend
- 1 API Gateway
- 3 microservicios (Users, Orders, Payments)
- 1 IdP

Responde:

- ¿Dónde se valida el token?
- ¿Qué headers pasa el gateway a los servicios?
- ¿Cómo se autorizan acciones en cada servicio?

### Ejercicio 2: Flujo de Request Completo

📝 [Ver ejercicio](./ejercicios/ejercicio-2-flujo-request.md)

Documenta paso a paso qué ocurre cuando:

```
POST /api/orders
Authorization: Bearer eyJhbGc...
{ "productId": 123, "quantity": 2 }
```

Desde que llega al gateway hasta que responde el servicio Orders.

---

## Checklist de Diseño

Al diseñar tu arquitectura de seguridad, verifica:

- [ ] **Identity Provider separado** (no mezclar con aplicaciones)
- [ ] **Gateway valida tokens** (firma, exp, iss, aud como mínimo)
- [ ] **Servicios validan autorización fina** (no confiar solo en gateway)
- [ ] **JWKS endpoint configurado** con cache y refresh
- [ ] **Rotación de claves planificada** (al menos cada 6 meses)
- [ ] **Logging centralizado** con trace-id en todos los componentes
- [ ] **Tokens short-lived** (access token < 15 min ideal)
- [ ] **Refresh tokens seguros** (httpOnly cookies o backend storage)
- [ ] **Rate limiting** implementado en gateway
- [ ] **mTLS entre servicios** (opcional pero recomendado)

---

## Errores Comunes

❌ **Confiar ciegamente en el gateway**

- Si el gateway tiene un bug, todo el sistema es vulnerable
- Los servicios deben validar al menos roles/scopes

❌ **Pasar el token completo entre servicios internos**

- Incrementa el payload innecesariamente
- Riesgo de leakage si se loguea
- Usa claims mínimos en headers

❌ **No planear rotación de claves**

- Cuando rotes claves, todos los tokens antiguos fallarán
- Necesitas soporte para múltiples claves activas (key rotation grace period)

❌ **Hardcodear URLs del IdP**

- Usa discovery endpoint (`.well-known/openid-configuration`)
- Facilita cambios de ambiente (dev/staging/prod)

---

## Recursos Adicionales

- 📄 [Comparación de API Gateways](./recursos/gateways.md)
- 📄 [Comparación de Service Meshes](./recursos/service-mesh.md)
- 📄 [Implementaciones de referencia](./recursos/ejemplos.md)

---

## Evaluación

Al completar este bloque deberías poder:

- [ ] Diseñar una arquitectura de seguridad completa para microservicios
- [ ] Decidir qué componente valida qué (separación de responsabilidades)
- [ ] Configurar un API Gateway para validación de tokens
- [ ] Implementar autorización fina en servicios
- [ ] Planear rotación de claves y manejo de JWKS
- [ ] Diseñar logging y auditoría distribuida

---

[⬅️ Bloque anterior: Protocolos](../bloque-2-protocolos/README.md) | [Volver al índice principal](../../README.md) | [Siguiente: Bloque 4 - Implementación ➡️](../bloque-4-implementacion/README.md)

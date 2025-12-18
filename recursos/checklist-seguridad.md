# Checklist de Seguridad para Autenticación y Autorización

Usa esta checklist para validar tu implementación antes de ir a producción.

---

## ✅ Diseño de Tokens

### Access Tokens
- [ ] Tokens **short-lived** (recomendado: 5-15 minutos)
- [ ] Firmas **asimétricas** (RS256) si múltiples validadores
- [ ] Claims mínimos necesarios (no información sensible innecesaria)
- [ ] Claim `aud` (audience) configurado correctamente
- [ ] Claim `iss` (issuer) validado en todos los servicios
- [ ] Claim `exp` (expiration) validado estrictamente
- [ ] No incluir passwords o secretos en el payload

### Refresh Tokens
- [ ] Solo emitir cuando sea necesario
- [ ] Almacenados en **httpOnly** secure cookies (no localStorage)
- [ ] O almacenados server-side con referencia al cliente
- [ ] Implementar **refresh token rotation**
- [ ] Lifetime razonable (ej: 7-30 días máximo)
- [ ] Revocar al logout
- [ ] Revocar al cambio de contraseña

### ID Tokens (OIDC)
- [ ] Solo para información del usuario (no para autorización)
- [ ] Validar `nonce` para prevenir replay attacks
- [ ] No usar como access token

### JWKS (JSON Web Key Set)
- [ ] JWKS endpoint público y accesible
- [ ] Cache de claves públicas con TTL razonable (1 hora)
- [ ] Soporte para rotación de claves (múltiples keys activas)
- [ ] Plan de rollover de claves documentado

---

## ✅ API Gateway / Middleware

### Validación de Tokens
- [ ] Verificar **firma** (signature) con clave pública
- [ ] Validar `iss` (issuer) contra whitelist
- [ ] Validar `aud` (audience) es correcto
- [ ] Validar `exp` (expiration) - rechazar tokens expirados
- [ ] Validar `nbf` (not before) si está presente
- [ ] Implementar **cache de tokens revocados** (blacklist)
- [ ] Timeout corto en llamadas a JWKS (no bloquear requests)

### Rate Limiting
- [ ] Implementado por client_id o user_id
- [ ] Límites diferentes por tier (free/pro/enterprise)
- [ ] Respuesta HTTP 429 (Too Many Requests) correcta
- [ ] Headers `X-RateLimit-*` informativos

### Headers y Propagación
- [ ] Propagar solo claims necesarios a servicios downstream
- [ ] Usar headers custom (ej: `X-User-Id`, `X-Scopes`)
- [ ] No pasar el token JWT completo si evitable
- [ ] Incluir `X-Request-ID` para correlación de logs

### Logging y Auditoría
- [ ] Loguear **todos** los fallos de autenticación
- [ ] Loguear accesos denegados (403)
- [ ] **No loguear** tokens completos (solo últimos 4 caracteres)
- [ ] Incluir trace-id en todos los logs
- [ ] Logs estructurados (JSON) para análisis

### CORS y Cookies
- [ ] CORS configurado correctamente (no `*` en producción)
- [ ] Cookies con `SameSite=Strict` o `Lax`
- [ ] Cookies con `Secure=true` (solo HTTPS)
- [ ] Cookies con `HttpOnly=true` para tokens

---

## ✅ Servicios (Microservicios)

### Validación Secundaria
- [ ] Servicios **NO confían ciegamente** en el gateway
- [ ] Al menos validar `exp` y `aud` localmente
- [ ] Validar scopes/roles necesarios para cada endpoint
- [ ] Autorización **resource-level** (no solo role-based)

### Autorización
- [ ] Implementar **principio de mínimo privilegio**
- [ ] Scopes bien definidos (ej: `read:orders`, `write:orders`)
- [ ] Roles granulares (evitar "super admin" omnipotente)
- [ ] Validar ownership de recursos (ej: user solo edita SUS datos)
- [ ] Integración con PDP (OPA) para políticas complejas

### Manejo de Errores
- [ ] Devolver **401** si token inválido/expirado
- [ ] Devolver **403** si autenticado pero sin permisos
- [ ] No exponer detalles internos en mensajes de error
- [ ] Loguear errores con contexto completo

---

## ✅ Identity Provider (IdP)

### Configuración
- [ ] Contraseñas con **política fuerte** (min 8 chars, complejidad)
- [ ] **MFA** habilitado (al menos para admins)
- [ ] Lockout de cuenta después de N intentos fallidos
- [ ] Sesiones con timeout razonable (ej: 1 hora inactividad)
- [ ] HTTPS obligatorio (no HTTP en producción)

### Clients
- [ ] Clients públicos (SPAs) usan **PKCE obligatoriamente**
- [ ] Clients confidenciales usan `client_secret` seguro
- [ ] Redirect URIs **estrictamente** whitelisted (no wildcards amplios)
- [ ] Post-logout URIs configurados
- [ ] Scopes por client restrictivos (solo los necesarios)

### Endpoints
- [ ] **Discovery endpoint** (`.well-known/openid-configuration`) accesible
- [ ] **JWKS endpoint** público y cacheado
- [ ] **Introspection endpoint** protegido (solo servicios autorizados)
- [ ] **Revocation endpoint** implementado y testeado

---

## ✅ OAuth2 / OIDC Flows

### Authorization Code + PKCE
- [ ] **PKCE obligatorio** para SPAs y mobile apps
- [ ] `code_challenge_method=S256` (SHA-256, no plain)
- [ ] `state` parameter para CSRF protection
- [ ] `nonce` en OIDC para replay protection
- [ ] Authorization codes **single-use** y short-lived (<10 min)

### Client Credentials
- [ ] Solo para comunicación M2M (machine-to-machine)
- [ ] Client secrets rotados periódicamente
- [ ] Scopes limitados (no user-level access)

### Flujos Obsoletos (NO USAR)
- [ ] ❌ **Implicit flow** deshabilitado
- [ ] ❌ **Resource Owner Password** deshabilitado
- [ ] ❌ Usar PKCE en lugar de Implicit

---

## ✅ Almacenamiento de Tokens (Cliente)

### SPAs (Single Page Apps)
- [ ] **NO** usar `localStorage` para tokens (vulnerable a XSS)
- [ ] Preferir **httpOnly cookies** + backend (BFF pattern)
- [ ] O usar memoria + refresh token en httpOnly cookie
- [ ] Implementar auto-refresh antes de expiración

### Mobile Apps
- [ ] Usar **Keychain** (iOS) o **Keystore** (Android)
- [ ] No almacenar en SharedPreferences sin encriptar
- [ ] Implementar biometric authentication si disponible

### Server-side (Backend)
- [ ] Tokens en cache con TTL = token expiration
- [ ] Encriptar si se persisten en BD
- [ ] Limpiar tokens al logout

---

## ✅ Operaciones y Monitoreo

### Rotación de Claves
- [ ] Plan documentado de rotación de claves (cada 6-12 meses)
- [ ] Proceso automatizado con grace period
- [ ] Múltiples claves activas durante rollover
- [ ] Rollback plan en caso de error

### Monitoreo
- [ ] Métricas de **tasa de fallos de autenticación** (alertar si spike)
- [ ] Latencia de validación de tokens (p50, p95, p99)
- [ ] Uso de refresh tokens (detectar abuso)
- [ ] Rate de revocaciones (anomalías)
- [ ] Health checks de IdP y JWKS endpoint

### Auditoría
- [ ] Eventos críticos logueados:
  - [ ] Login success/failure
  - [ ] Token refresh
  - [ ] Token revocation
  - [ ] Authorization denials
  - [ ] Cambios de password
  - [ ] Cambios de permisos/roles
- [ ] Logs **inmutables** (append-only)
- [ ] Retención de logs adecuada (ej: 90 días)

### Incident Response
- [ ] Procedimiento documentado para **token leakage**
- [ ] Capacidad de revocar **todos** los tokens de un usuario
- [ ] Capacidad de revocar tokens por client_id
- [ ] Contactos de seguridad definidos

---

## ✅ Testing

### Unit Tests
- [ ] Tests de validación de JWT (firma, claims, expiración)
- [ ] Tests de autorización (scopes, roles, ownership)
- [ ] Tests de rate limiting
- [ ] Tests de manejo de errores

### Integration Tests
- [ ] Flujo completo de Authorization Code + PKCE
- [ ] Flujo de Client Credentials
- [ ] Refresh token flow
- [ ] Revocation flow
- [ ] Logout flow

### Security Tests
- [ ] **Token tampering** (modificar payload) → debe fallar
- [ ] **Expired token** → debe devolver 401
- [ ] **Token replay** (reuso de authorization code) → debe fallar
- [ ] **CSRF** (state parameter inválido) → debe fallar
- [ ] **XSS** prevention (sanitizar inputs)
- [ ] **Broken Object Level Authorization** (BOLA) tests
- [ ] Pentesting básico con OWASP ZAP o Burp Suite

---

## ✅ Compliance y Legal

### GDPR / Privacidad
- [ ] No loguear PII innecesaria
- [ ] Capacidad de **eliminar** todos los datos de un usuario
- [ ] Logs anonimizados o pseudonimizados cuando posible
- [ ] Política de retención de datos documentada

### Regulaciones Específicas
- [ ] HIPAA (salud): encripción en tránsito y reposo
- [ ] PCI-DSS (pagos): no almacenar datos de tarjetas
- [ ] SOC 2: auditoría y logging completo

---

## 🔍 Revisión Pre-Producción

Antes de ir a producción, verifica que **TODOS** los items marcados:

- [ ] **Diseño de Tokens**: ✅ completado
- [ ] **API Gateway**: ✅ completado
- [ ] **Servicios**: ✅ completado
- [ ] **Identity Provider**: ✅ completado
- [ ] **OAuth2 Flows**: ✅ completado
- [ ] **Almacenamiento**: ✅ completado
- [ ] **Operaciones**: ✅ completado
- [ ] **Testing**: ✅ completado
- [ ] **Compliance**: ✅ completado

---

## 📚 Referencias

- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/)
- [OAuth 2.0 Security Best Practices](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics)
- [JWT Best Practices](https://datatracker.ietf.org/doc/html/rfc8725)

---

[⬅️ Volver a Recursos](./README.md)

# Laboratorios Prácticos (Hands-On Labs)

Este directorio contiene los 5 laboratorios principales del curso, diseñados para aplicar los conceptos teóricos en implementaciones reales.

---

## 📋 Lista de Labs

### [Lab 1: Keycloak + OAuth2 + PKCE](./lab-1-keycloak-oauth-pkce/)
**Duración**: 2-3 horas  
**Nivel**: Principiante-Intermedio  
**Objetivos**:
- Instalar y configurar Keycloak con Docker
- Crear realm, clients y roles
- Implementar Authorization Code + PKCE desde una SPA
- Recibir y decodificar JWT tokens

**Stack**: Keycloak, Docker, JavaScript/React (o cualquier SPA)

---

### [Lab 2: API Gateway + Validación JWT](./lab-2-gateway-jwt-validation/)
**Duración**: 2-3 horas  
**Nivel**: Intermedio  
**Objetivos**:
- Crear API Gateway con YARP (.NET) o alternativa
- Implementar middleware de validación JWT
- Consumir JWKS endpoint
- Extraer claims y propagar a servicios downstream

**Stack**: .NET 8 (YARP), JWT, Keycloak

---

### [Lab 3: OPA + Políticas ABAC](./lab-3-opa-abac-policies/)
**Duración**: 2-3 horas  
**Nivel**: Intermedio-Avanzado  
**Objetivos**:
- Instalar Open Policy Agent
- Escribir políticas en Rego
- Implementar autorización basada en atributos (ABAC)
- Integrar OPA con microservicios .NET

**Stack**: OPA, Rego, .NET 8, Docker

---

### [Lab 4: Refresh Tokens y Revocación](./lab-4-refresh-revocation/)
**Duración**: 2 horas  
**Nivel**: Intermedio  
**Objetivos**:
- Implementar refresh token flow
- Almacenamiento seguro (httpOnly cookies)
- Crear endpoint de revocación
- Implementar logout completo

**Stack**: .NET 8, Keycloak, JavaScript

---

### [Lab 5: Security Testing](./lab-5-security-testing/)
**Duración**: 2-3 horas  
**Nivel**: Avanzado  
**Objetivos**:
- Tests de seguridad: token tampering, replay, CSRF
- Unit tests de validación de tokens
- Integration tests de flujos OAuth2
- Pentesting básico con herramientas

**Stack**: .NET 8, xUnit, Postman/Newman, OWASP ZAP

---

## 🎯 Orden Recomendado

Si es la primera vez que trabajas con estos conceptos, sigue el orden numérico:

1. **Lab 1** → Fundamentos de OAuth2 y OIDC
2. **Lab 2** → Validación y autorización en gateway
3. **Lab 3** → Políticas avanzadas con OPA
4. **Lab 4** → Gestión de tokens de larga duración
5. **Lab 5** → Asegurar y probar el sistema completo

---

## 📦 Prerequisitos Generales

Antes de comenzar los labs, asegúrate de tener instalado:

- **Docker Desktop** (para Keycloak, OPA, PostgreSQL)
- **.NET 8 SDK** (para gateway y microservicios)
- **Node.js 18+** (para SPAs de ejemplo)
- **Git** (para clonar repositorios de ejemplo)
- **Postman** o **curl** (para probar endpoints)
- **Visual Studio Code** o tu IDE preferido

---

## 🚀 Quick Start

Cada lab tiene su propia carpeta con:
- `README.md` - Instrucciones paso a paso
- `src/` - Código fuente completo
- `docker-compose.yml` - Servicios necesarios
- `postman/` - Colección Postman con requests de prueba
- `docs/` - Documentación adicional y screenshots

Para empezar un lab:

```bash
cd labs/lab-1-keycloak-oauth-pkce
docker-compose up -d
# Seguir instrucciones en README.md
```

---

## 💡 Consejos para el Aprendizaje

1. **No copies-pegues ciegamente**: entiende cada línea de código
2. **Experimenta**: prueba modificar valores y observa qué pasa
3. **Usa debugger**: coloca breakpoints en middleware de validación
4. **Lee los logs**: configuramos logging detallado para aprender
5. **Haz troubleshooting**: si algo falla, es una oportunidad de aprender

---

## 🔍 Solución de Problemas Comunes

### Error: "Token signature invalid"
- Verifica que el JWKS endpoint esté accesible
- Revisa que el `iss` del token coincida con la configuración
- Asegúrate de usar la clave pública correcta

### Error: "docker-compose not found"
- Instala Docker Desktop
- En Windows, reinicia después de instalar

### Error: "Port 8080 already in use"
- Cambia el puerto en `docker-compose.yml`
- O detén el servicio que usa ese puerto

---

## 📚 Recursos Adicionales

- [Documentación de Keycloak](https://www.keycloak.org/documentation)
- [OPA Playground](https://play.openpolicyagent.org/)
- [JWT.io - Decodificador de tokens](https://jwt.io/)
- [OAuth 2.0 Playground](https://www.oauth.com/playground/)

---

## ✅ Certificación de Completitud

Considera un lab completado cuando:
- [ ] Puedes ejecutar todo el flujo sin errores
- [ ] Entiendes cada componente y su responsabilidad
- [ ] Puedes explicar el código a otra persona
- [ ] Has experimentado con variaciones (cambiar scopes, roles, etc.)
- [ ] Has documentado tus aprendizajes clave

---

[⬅️ Volver al índice principal](../README.md)

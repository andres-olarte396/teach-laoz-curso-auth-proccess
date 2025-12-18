# 🔐 Curso: Autenticación de APIs y Autorización - Seguridad de APIs

> Aprende a implementar autenticación y autorización robustas en arquitecturas de microservicios usando OAuth2, OpenID Connect, JWT y políticas avanzadas de seguridad.

---

## 📖 Sobre este Curso

Este curso te enseña desde los fundamentos conceptuales hasta la implementación práctica de sistemas completos de autenticación y autorización para APIs modernas. 

**Stack principal**: .NET 8, Keycloak, OPA, YARP, Docker

**Alternativas cubiertas**: Node.js, Java Spring Boot, Auth0/Okta

---

## 🎯 Objetivos del Curso

Al completar este curso serás capaz de:

✅ Diseñar sistemas de autenticación y autorización seguros  
✅ Implementar OAuth2 y OpenID Connect correctamente  
✅ Validar y gestionar JWT tokens  
✅ Aplicar políticas RBAC y ABAC en microservicios  
✅ Usar Open Policy Agent (OPA) para decisiones de autorización  
✅ Configurar API Gateways con validación de tokens  
✅ Implementar refresh tokens y revocación  
✅ Realizar testing de seguridad en flujos de autenticación  
✅ Operar sistemas auth en producción con rotación de claves  

---

## 📚 Contenido del Curso

### [Bloque 1: Fundamentos](./modulos/bloque-1-fundamentos/)
**Duración**: 4-6 horas

- Autenticación vs Autorización
- Identidad digital: subjects, claims, tokens
- Modelos de autorización: RBAC, ABAC, PBAC
- Principios de seguridad

**🎯 Actividades**: Diseñar políticas RBAC/ABAC, diagramar flujos de auth

---

### [Bloque 2: Protocolos y Formatos](./modulos/bloque-2-protocolos/)
**Duración**: 8-10 horas

- OAuth 2.0: grants (Authorization Code, PKCE, Client Credentials)
- OpenID Connect: ID tokens vs Access tokens
- JWT: estructura, firma, validación
- Token introspection y revocation
- Seguridad: PKCE, CORS, SameSite

**🎯 Actividades**: Configurar IdP, implementar PKCE, validar JWT

---

### [Bloque 3: Diseño en Microservicios](./modulos/bloque-3-diseno-microservicios/)
**Duración**: 6-8 horas

- Arquitectura de seguridad distribuida
- Componentes: IdP, Gateway, Service Mesh, PDP
- Patrones: Token Relay, BFF, Claims Enrichment
- Gestión de claves (JWKS)
- Auditoría y observabilidad

**🎯 Actividades**: Diseñar arquitectura completa, flujos end-to-end

---

### [Bloque 4: Implementación Práctica](./modulos/bloque-4-implementacion/)
**Duración**: 12-16 horas (hands-on intensivo)

**Paso a paso**:
1. Levantar Keycloak con Docker
2. Configurar OAuth2 flows (PKCE, Client Credentials)
3. Implementar API Gateway con YARP
4. Crear microservicios con autorización
5. Refresh tokens y revocación
6. Auditoría y logging (Seq, Grafana)
7. Operaciones: rotación de claves
8. Security testing

**🎯 Resultado**: Sistema completo funcional con docker-compose

---

### [Bloque 5: Políticas Avanzadas](./modulos/bloque-5-politicas-avanzadas/)
**Duración**: 6-8 horas

- ABAC avanzado (atributos de usuario, recurso, contexto)
- User-Managed Access (UMA)
- OAuth2 Token Exchange (RFC 8693)
- On-behalf-of flows
- Políticas contextuales (geo, tiempo, dispositivo)
- Fine-grained authorization

**🎯 Casos de uso reales**: Sistema de salud, multi-tenant SaaS, banking

---

## 🧪 Laboratorios Prácticos

### [Lab 1: Keycloak + OAuth2 + PKCE](./labs/lab-1-keycloak-oauth-pkce/)
Configura Keycloak, implementa Authorization Code + PKCE desde una SPA.

### [Lab 2: API Gateway + Validación JWT](./labs/lab-2-gateway-jwt-validation/)
Crea gateway con YARP, valida tokens, consume JWKS.

### [Lab 3: OPA + Políticas ABAC](./labs/lab-3-opa-abac-policies/)
Escribe políticas Rego, integra OPA con microservicios.

### [Lab 4: Refresh Tokens y Revocación](./labs/lab-4-refresh-revocation/)
Implementa refresh flow completo y endpoints de revocación.

### [Lab 5: Security Testing](./labs/lab-5-security-testing/)
Tests de seguridad: tampering, replay, CSRF, pentesting básico.

📁 [Ver todos los labs](./labs/)

---

## 💻 Ejemplos de Código

Código fuente listo para usar:

- [Gateway YARP](./ejemplos-codigo/gateway-yarp/) - API Gateway con validación JWT
- [Middleware JWT](./ejemplos-codigo/middleware-jwt/) - Validación de tokens reutilizable
- [Políticas OPA](./ejemplos-codigo/opa-policies/) - Ejemplos Rego para RBAC/ABAC
- [Microservicios](./ejemplos-codigo/microservices/) - 3 servicios .NET con autorización

📁 [Ver todos los ejemplos](./ejemplos-codigo/)

---

## 📋 Recursos y Checklists

- ✅ [**Checklist de Seguridad**](./recursos/checklist-seguridad.md) - Validación pre-producción
- 📖 [Glosario de Términos](./recursos/glosario.md)
- 📚 [Referencias y Especificaciones](./recursos/referencias.md)
- 🛠️ [Herramientas Recomendadas](./recursos/herramientas.md)
- ⚠️ [Errores Comunes](./recursos/errores-comunes.md)
- ❓ [FAQ](./recursos/faq.md)

📁 [Ver todos los recursos](./recursos/)

---

## 🗺️ Roadmap Original

El contenido de este curso está basado en el [ROADMAP.md](./ROADMAP.md) original que contiene:

- Visión general del curso
- 5 bloques temáticos detallados
- Checklists accionables
- 5 mini-labs
- Errores comunes y críticas
- Ejercicios concretos
- Métricas y señales de alarma

---

## 🚀 Cómo Empezar

### Prerequisitos

- **Docker Desktop** (para Keycloak, OPA, etc.)
- **.NET 8 SDK** (para ejemplos en C#)
- **Node.js 18+** (para SPAs de ejemplo)
- **Git**
- **Editor**: VS Code recomendado

### Ruta de Aprendizaje Recomendada

**Nivel Principiante**:
1. Bloque 1: Fundamentos (conceptos)
2. Bloque 2: Protocolos (OAuth2, JWT)
3. Lab 1: Keycloak + PKCE

**Nivel Intermedio**:
4. Bloque 3: Diseño en Microservicios
5. Bloque 4: Implementación (paso a paso)
6. Labs 2-4: Gateway, OPA, Refresh tokens

**Nivel Avanzado**:
7. Bloque 5: Políticas Avanzadas
8. Lab 5: Security Testing
9. Implementar casos de uso reales

### Quick Start

```bash
# Clonar el repositorio
git clone <url-del-repo>
cd teach-auth-proccess

# Empezar con el primer lab
cd labs/lab-1-keycloak-oauth-pkce
docker-compose up -d

# Seguir instrucciones en labs/lab-1-keycloak-oauth-pkce/README.md
```

---

## 📊 Estructura del Proyecto

```
teach-auth-proccess/
├── README.md                          # Este archivo
├── ROADMAP.md                         # Roadmap original detallado
│
├── modulos/                           # Contenido teórico por bloques
│   ├── bloque-1-fundamentos/
│   ├── bloque-2-protocolos/
│   ├── bloque-3-diseno-microservicios/
│   ├── bloque-4-implementacion/
│   └── bloque-5-politicas-avanzadas/
│
├── labs/                              # 5 laboratorios hands-on
│   ├── lab-1-keycloak-oauth-pkce/
│   ├── lab-2-gateway-jwt-validation/
│   ├── lab-3-opa-abac-policies/
│   ├── lab-4-refresh-revocation/
│   └── lab-5-security-testing/
│
├── ejemplos-codigo/                   # Código fuente reutilizable
│   ├── gateway-yarp/
│   ├── middleware-jwt/
│   ├── opa-policies/
│   └── microservices/
│
├── diagramas/                         # Diagramas de arquitectura
│
└── recursos/                          # Documentación de referencia
    ├── checklist-seguridad.md
    ├── glosario.md
    ├── referencias.md
    ├── herramientas.md
    ├── errores-comunes.md
    └── faq.md
```

---

## 🎓 Certificación de Completitud

Considera el curso completado cuando puedes:

- [ ] Explicar diferencias entre AuthN y AuthZ
- [ ] Implementar OAuth2 Authorization Code + PKCE
- [ ] Validar JWT correctamente (firma + claims)
- [ ] Diseñar arquitectura de auth para microservicios
- [ ] Configurar y usar Keycloak
- [ ] Implementar API Gateway con YARP
- [ ] Escribir políticas OPA en Rego
- [ ] Implementar refresh tokens y revocación
- [ ] Realizar pentesting básico de flujos auth
- [ ] Deployar sistema completo con Docker Compose
- [ ] Aplicar checklist de seguridad completo

---

## 🤝 Contribuciones

Este es un proyecto educativo en evolución. Sugerencias y mejoras son bienvenidas:

1. Reporta errores o inconsistencias
2. Propón mejoras al contenido
3. Añade ejemplos adicionales
4. Traduce contenido a otros idiomas

---

## 📄 Licencia

[Definir licencia - MIT, Apache 2.0, etc.]

---

## 📞 Contacto y Soporte

- **Issues**: [GitHub Issues]
- **Discusiones**: [GitHub Discussions]
- **Email**: [tu-email@dominio.com]

---

## 🌟 Recursos Externos Recomendados

### Documentación Oficial
- [OAuth 2.0 - RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749)
- [JWT - RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519)
- [PKCE - RFC 7636](https://datatracker.ietf.org/doc/html/rfc7636)
- [OpenID Connect](https://openid.net/connect/)
- [Keycloak Documentation](https://www.keycloak.org/documentation)
- [Open Policy Agent](https://www.openpolicyagent.org/)

### Herramientas
- [jwt.io](https://jwt.io) - Decodificador JWT
- [OAuth 2.0 Playground](https://www.oauth.com/playground/)
- [OPA Playground](https://play.openpolicyagent.org/)

### Seguridad
- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/)
- [OAuth 2.0 Security Best Practices](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics)

---

## ⭐ Si este curso te resulta útil

- Dale una estrella ⭐ al repositorio
- Compártelo con otros desarrolladores
- Déjanos feedback para mejorar

---

**¡Bienvenido al curso! Comienza tu viaje hacia la maestría en autenticación y autorización de APIs.**

[🚀 Empezar con Bloque 1: Fundamentos](./modulos/bloque-1-fundamentos/) | [📋 Ver Roadmap Completo](./ROADMAP.md)

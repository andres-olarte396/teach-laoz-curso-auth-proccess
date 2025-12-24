# Bloque 1 — Fundamentos de Autenticación y Autorización

## Objetivos del Bloque

Dominar las definiciones y relación entre autenticación (AuthN) y autorización (AuthZ), comprender los diferentes modelos de autorización y establecer las bases conceptuales para implementar sistemas seguros.

## Duración estimada

4-6 horas

---

## Contenido

### 1. [Autenticación vs Autorización](./01-authn-vs-authz.md)

- Diferencias fundamentales
- Responsabilidades de cada componente
- Ejemplos prácticos

### 2. [Identidad Digital](./02-identidad-digital.md)

- Subject, claims y atributos
- Sessions vs Token-based authentication
- Ciclo de vida de identidades

### 3. [Modelos de Autorización](./03-modelos-autorizacion.md)

- RBAC (Role-Based Access Control)
- ABAC (Attribute-Based Access Control)
- PBAC (Policy-Based Access Control)
- ACL (Access Control Lists)
- Comparación y casos de uso

### 4. [Principios de Seguridad](./04-principios-seguridad.md)

- Principio de mínimo privilegio
- Separación de responsabilidades
- Defensa en profundidad
- Confidencialidad, Integridad, No repudio

---

## Actividades Prácticas

### Ejercicio 1. Diseño de Políticas RBAC

📝 [Ver ejercicio](./ejercicios/ejercicio-1-rbac.md)

Diseña un sistema RBAC para una aplicación de gestión de proyectos con:

- 3 roles (Admin, Project Manager, Developer)
- 5 recursos (Projects, Tasks, Users, Reports, Settings)
- Define permisos para cada rol

### Ejercicio 2: Diseño de Políticas ABAC

📝 [Ver ejercicio](./ejercicios/ejercicio-2-abac.md)

Amplía el sistema anterior con políticas ABAC que consideren:

- Atributos del usuario (departamento, antigüedad)
- Atributos del recurso (estado, confidencialidad)
- Contexto (horario, ubicación)

### Ejercicio 3: Diagrama de Flujo

📝 [Ver ejercicio](./ejercicios/ejercicio-3-flujo.md)

Dibuja el flujo completo de una petición autenticada:

```
Cliente → Gateway → Servicio → Base de datos
```

Identifica en qué punto ocurre:

- Autenticación
- Autorización (primaria)
- Autorización (secundaria/fina)

---

## Recursos Adicionales

- 📄 [Glosario de términos](./recursos/glosario.md)
- 📚 [Lecturas recomendadas](./recursos/lecturas.md)
- 🎥 [Videos complementarios](./recursos/videos.md)

---

## Evaluación

Al completar este bloque deberías poder:

- [ ] Explicar claramente la diferencia entre AuthN y AuthZ
- [ ] Identificar qué modelo de autorización usar según el caso
- [ ] Diseñar políticas RBAC y ABAC básicas
- [ ] Aplicar principios de seguridad en diseño de sistemas
- [ ] Diagramar flujos de autenticación/autorización

---

[⬅️ Volver al índice principal](../../README.md) | [Siguiente: Bloque 2 - Protocolos ➡️](../bloque-2-protocolos/README.md)

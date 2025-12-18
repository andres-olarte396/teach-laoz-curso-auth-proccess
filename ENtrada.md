# Autenticación vs. Autorización: ¿Quién Eres y Qué Puedes Hacer?

## 🎯 Introducción

Imagina que estás en un aeropuerto. Al llegar al control de seguridad, muestras tu pasaporte o DNI para que verifiquen que eres la persona que dices ser. Este primer paso es la **autenticación**: demuestras tu identidad. 

Una vez dentro, quieres acceder a la sala VIP. Para ello, muestras tu tarjeta de embarque platinum. Este segundo paso es la **autorización**: demuestras que tienes los permisos necesarios para entrar a esa área específica. 

En el mundo digital, la seguridad funciona de manera muy similar, basándose en estos dos conceptos fundamentales que, aunque están relacionados, responden a preguntas muy diferentes.

---

## 🔑 Autenticación (AuthN): Verificando tu Identidad

### ¿Qué es?

La **Autenticación** es el proceso de verificar la identidad de un usuario, sistema o entidad. Su único objetivo es confirmar que alguien o algo es realmente quien dice ser.

### Pregunta clave

> **"¿Quién eres?"**

Este proceso es una parte fundamental de nuestra vida digital diaria.

### Ejemplos que usas todos los días

- **Ingresar tu usuario y contraseña** en un sitio web para acceder a tu cuenta personal
- **Desbloquear tu teléfono** con tu huella dactilar o reconocimiento facial
- **Usar autenticación de dos factores (2FA)**, donde después de tu contraseña, ingresas un código de tu teléfono para una capa extra de seguridad

Una vez que el sistema ha verificado con éxito quién eres, el siguiente paso es determinar qué se te permite hacer.

---

## 🔐 Autorización (AuthZ): Definiendo tus Permisos

### ¿Qué es?

La **Autorización** es el proceso que ocurre después de una autenticación exitosa. Determina qué acciones, recursos o datos puede ver o modificar un usuario ya identificado.

### Pregunta clave

> **"¿Qué puedes hacer?"**

La autorización define las reglas y los límites de acceso dentro de un sistema.

### Ejemplos que encuentras a diario

- **Ver contenido premium en Netflix**, lo cual solo es posible si tu cuenta (ya autenticada) tiene una suscripción de pago activa
- **Editar un documento compartido**, algo que solo puedes hacer si el propietario del documento te ha concedido explícitamente permisos de edición
- **Acceder a la sala VIP del aeropuerto**, un privilegio que solo se te concede al mostrar tu tarjeta de embarque platinum

Ahora que hemos visto cada concepto por separado, comparemos sus diferencias clave cara a cara para consolidar el aprendizaje.

---

## 📊 Comparación Directa: Las Diferencias Clave

La siguiente tabla resume las distinciones más importantes entre autenticación y autorización para que puedas verlas de un solo vistazo.

| Aspecto | Autenticación (AuthN) | Autorización (AuthZ) |
|---------|----------------------|---------------------|
| **Pregunta** | ¿Quién eres? | ¿Qué puedes hacer? |
| **Proceso** | Verificación de identidad | Verificación de permisos |
| **Cuándo ocurre** | Al inicio de la sesión | En cada acción/recurso |
| **Información usada** | Credenciales (usuario/pass, tokens, biometría) | Roles, permisos, políticas |
| **Resultado** | Usuario identificado | Acción permitida o denegada |
| **Ejemplo** | Login con usuario y contraseña | Admin puede eliminar usuarios |

Es fundamental recordar que estos procesos siempre ocurren en un orden específico, como veremos en un ejemplo práctico.

---

## 🔄 El Flujo Típico: Primero Autenticación, Luego Autorización

En cualquier sistema seguro, la **autenticación siempre debe ocurrir antes que la autorización**. Es lógicamente imposible conceder permisos si el sistema no sabe primero quién está haciendo la solicitud. El puente entre estos dos pasos es el **token de acceso**.

### El flujo típico sigue estos pasos

**1. Primero, te Autenticas y Recibes un "Pase"**

El usuario se identifica con sus credenciales. Si son correctas, el sistema lo autentica y le emite un **token de acceso** (como un pase de backstage digital).

**2. Luego, Usas tu "Pase" para Ser Autorizado**

Para cada acción posterior (ej. eliminar un archivo), el usuario presenta su token de acceso. El sistema:
- Primero **valida el token** para confirmar la identidad
- Luego **comprueba si los permisos** asociados a ese token le permiten realizar la acción solicitada (autorización)

Esta distinción también se refleja en los códigos de error que las APIs nos devuelven cuando algo sale mal.

---

## ⚠️ Entendiendo los Errores: 401 vs. 403

Cuando interactúas con aplicaciones y APIs, los códigos de error HTTP te dan pistas importantes sobre si el problema es de autenticación o de autorización. Los dos más comunes son el **401 Unauthorized** y el **403 Forbidden**.

| Código de Error | Significado Común | ¿Qué falló? |
|----------------|-------------------|-------------|
| **401 Unauthorized** | "No estás identificado" | **Falló la Autenticación**. El sistema no pudo verificar tu identidad porque el token de acceso es inválido, está expirado o no fue proporcionado. |
| **403 Forbidden** | "No tienes permiso" | **Falló la Autorización**. El sistema sabe quién eres, pero no tienes los permisos necesarios para acceder a este recurso. |

---

## 📝 Resumen Final: Las Ideas Clave

Para recordar fácilmente la diferencia, quédate con estas dos ideas centrales:

- 🔑 **Autenticación (AuthN) = Identidad**  
  Responde a la pregunta: *"¿Quién eres?"*

- 🔐 **Autorización (AuthZ) = Permisos**  
  Responde a la pregunta: *"¿Qué puedes hacer?"*

> **Regla de oro:** Primero autenticamos, luego autorizamos.

---

## 🚀 Siguiente Paso

Ahora que comprendes la diferencia fundamental entre autenticación y autorización, estás listo para profundizar en:

- [Modelos de Autorización (RBAC, ABAC)](../modulos/bloque-1-fundamentos/03-modelos-autorizacion.md)
- [Protocolos de Autenticación (OAuth2, OIDC)](../modulos/bloque-2-protocolos/README.md)
- [Implementación Práctica con JWT](../labs/lab-1-keycloak-oauth-pkce/README.md)

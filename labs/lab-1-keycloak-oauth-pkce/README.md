# Lab 1: Keycloak + Authorization Code + PKCE

## 🎯 Objetivos

Al finalizar este lab podrás:
- Instalar y configurar Keycloak con Docker
- Crear un realm y configurar clients
- Implementar el flujo Authorization Code + PKCE
- Obtener, decodificar y validar JWT tokens
- Entender la diferencia entre ID Token y Access Token

**Duración estimada**: 2-3 horas

---

## 📋 Prerequisitos

- Docker Desktop instalado
- Node.js 18+ (para la SPA de prueba)
- Navegador moderno
- Editor de código (VS Code recomendado)
- Postman (opcional, para testing)

---

## 🏗️ Arquitectura del Lab

```
┌─────────────┐
│   Browser   │
│   (SPA)     │
└──────┬──────┘
       │
       │ 1. Authorization Code + PKCE
       ▼
┌─────────────────┐
│   Keycloak      │
│   Port: 8080    │
│   Realm: demo   │
└─────────────────┘
```

---

## 📝 Paso 1: Levantar Keycloak

### 1.1 Crear docker-compose.yml

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    container_name: keycloak-db
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: keycloak
      POSTGRES_PASSWORD: keycloak
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - keycloak-network

  keycloak:
    image: quay.io/keycloak/keycloak:23.0
    container_name: keycloak
    command: start-dev
    environment:
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://postgres:5432/keycloak
      KC_DB_USERNAME: keycloak
      KC_DB_PASSWORD: keycloak
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    networks:
      - keycloak-network

volumes:
  postgres_data:

networks:
  keycloak-network:
    driver: bridge
```

### 1.2 Iniciar servicios

```bash
docker-compose up -d
```

### 1.3 Verificar que Keycloak está corriendo

Navega a: http://localhost:8080

Credenciales:
- Usuario: `admin`
- Contraseña: `admin`

---

## 📝 Paso 2: Configurar Realm y Client

### 2.1 Crear Realm

1. En la consola de Keycloak, haz clic en el dropdown del realm (arriba a la izquierda)
2. Clic en **"Create Realm"**
3. Nombre: `demo`
4. **Create**

### 2.2 Crear Client para SPA

1. En el realm `demo`, ve a **Clients** → **Create client**
2. Configura:
   - **Client type**: OpenID Connect
   - **Client ID**: `spa-client`
   - **Next**
3. Capability config:
   - ✅ **Client authentication**: OFF (public client)
   - ✅ **Authorization**: OFF
   - ✅ **Standard flow**: ON (Authorization Code)
   - ❌ **Direct access grants**: OFF
   - ❌ **Implicit flow**: OFF
   - **Next**
4. Login settings:
   - **Valid redirect URIs**: `http://localhost:3000/*`
   - **Valid post logout redirect URIs**: `http://localhost:3000/*`
   - **Web origins**: `http://localhost:3000`
   - **Save**

### 2.3 Crear Usuario de Prueba

1. Ve a **Users** → **Add user**
2. Configura:
   - **Username**: `testuser`
   - **Email**: `test@example.com`
   - **First name**: Test
   - **Last name**: User
   - **Email verified**: ON
   - **Create**
3. En la pestaña **Credentials**:
   - **Set password**: `password123`
   - **Temporary**: OFF
   - **Save**

### 2.4 Crear Roles

1. Ve a **Realm roles** → **Create role**
2. Crea dos roles:
   - `user` (rol básico)
   - `admin` (rol administrativo)
3. Asigna rol al usuario:
   - Ve a **Users** → `testuser` → **Role mapping**
   - **Assign role** → selecciona `user`

---

## 📝 Paso 3: Implementar SPA con PKCE

### 3.1 Crear proyecto React simple

```bash
npx create-react-app keycloak-spa
cd keycloak-spa
npm install crypto-js
```

### 3.2 Implementar utilidades PKCE

Crea `src/pkce.js`:

```javascript
import CryptoJS from 'crypto-js';

// Generar code_verifier aleatorio
export function generateCodeVerifier() {
  const array = new Uint8Array(32);
  window.crypto.getRandomValues(array);
  return base64URLEncode(array);
}

// Generar code_challenge a partir del verifier
export function generateCodeChallenge(verifier) {
  const hash = CryptoJS.SHA256(verifier);
  return base64URLEncode(hash);
}

// Helper para encoding base64URL
function base64URLEncode(str) {
  return btoa(String.fromCharCode.apply(null, new Uint8Array(str)))
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=/g, '');
}

// Generar state para CSRF protection
export function generateState() {
  const array = new Uint8Array(16);
  window.crypto.getRandomValues(array);
  return base64URLEncode(array);
}
```

### 3.3 Implementar componente de Login

Crea `src/Auth.js`:

```javascript
import React, { useEffect, useState } from 'react';
import { generateCodeVerifier, generateCodeChallenge, generateState } from './pkce';

const KEYCLOAK_URL = 'http://localhost:8080';
const REALM = 'demo';
const CLIENT_ID = 'spa-client';
const REDIRECT_URI = 'http://localhost:3000/callback';

function Auth() {
  const [user, setUser] = useState(null);
  const [tokens, setTokens] = useState(null);

  useEffect(() => {
    // Verificar si estamos en el callback
    const params = new URLSearchParams(window.location.search);
    const code = params.get('code');
    const state = params.get('state');

    if (code && state) {
      handleCallback(code, state);
    }
  }, []);

  const login = () => {
    // Generar PKCE parameters
    const codeVerifier = generateCodeVerifier();
    const codeChallenge = generateCodeChallenge(codeVerifier);
    const state = generateState();

    // Guardar en sessionStorage
    sessionStorage.setItem('code_verifier', codeVerifier);
    sessionStorage.setItem('state', state);

    // Construir authorization URL
    const authUrl = new URL(`${KEYCLOAK_URL}/realms/${REALM}/protocol/openid-connect/auth`);
    authUrl.searchParams.append('client_id', CLIENT_ID);
    authUrl.searchParams.append('redirect_uri', REDIRECT_URI);
    authUrl.searchParams.append('response_type', 'code');
    authUrl.searchParams.append('scope', 'openid profile email');
    authUrl.searchParams.append('code_challenge', codeChallenge);
    authUrl.searchParams.append('code_challenge_method', 'S256');
    authUrl.searchParams.append('state', state);

    // Redirect
    window.location.href = authUrl.toString();
  };

  const handleCallback = async (code, state) => {
    // Validar state (CSRF protection)
    const savedState = sessionStorage.getItem('state');
    if (state !== savedState) {
      console.error('State mismatch - possible CSRF attack');
      return;
    }

    // Recuperar code_verifier
    const codeVerifier = sessionStorage.getItem('code_verifier');

    // Intercambiar código por tokens
    const tokenUrl = `${KEYCLOAK_URL}/realms/${REALM}/protocol/openid-connect/token`;
    const body = new URLSearchParams({
      grant_type: 'authorization_code',
      client_id: CLIENT_ID,
      redirect_uri: REDIRECT_URI,
      code: code,
      code_verifier: codeVerifier
    });

    try {
      const response = await fetch(tokenUrl, {
        method: 'POST',
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
        body: body
      });

      const tokens = await response.json();
      setTokens(tokens);

      // Decodificar ID token
      const idTokenPayload = JSON.parse(atob(tokens.id_token.split('.')[1]));
      setUser(idTokenPayload);

      // Limpiar sessionStorage
      sessionStorage.removeItem('code_verifier');
      sessionStorage.removeItem('state');

      // Limpiar URL
      window.history.replaceState({}, document.title, '/');
    } catch (error) {
      console.error('Error exchanging code for tokens:', error);
    }
  };

  const logout = () => {
    const logoutUrl = new URL(`${KEYCLOAK_URL}/realms/${REALM}/protocol/openid-connect/logout`);
    logoutUrl.searchParams.append('post_logout_redirect_uri', 'http://localhost:3000');
    logoutUrl.searchParams.append('id_token_hint', tokens.id_token);
    
    window.location.href = logoutUrl.toString();
  };

  return (
    <div style={{ padding: '20px' }}>
      <h1>Keycloak OAuth2 + PKCE Demo</h1>
      
      {!user ? (
        <button onClick={login}>Login con Keycloak</button>
      ) : (
        <div>
          <h2>¡Bienvenido, {user.name || user.preferred_username}!</h2>
          <button onClick={logout}>Logout</button>
          
          <h3>User Info:</h3>
          <pre>{JSON.stringify(user, null, 2)}</pre>
          
          <h3>Tokens:</h3>
          <div>
            <h4>ID Token:</h4>
            <textarea readOnly value={tokens.id_token} style={{ width: '100%', height: '100px' }} />
            
            <h4>Access Token:</h4>
            <textarea readOnly value={tokens.access_token} style={{ width: '100%', height: '100px' }} />
            
            {tokens.refresh_token && (
              <>
                <h4>Refresh Token:</h4>
                <textarea readOnly value={tokens.refresh_token} style={{ width: '100%', height: '100px' }} />
              </>
            )}
          </div>
        </div>
      )}
    </div>
  );
}

export default Auth;
```

### 3.4 Actualizar App.js

```javascript
import './App.css';
import Auth from './Auth';

function App() {
  return (
    <div className="App">
      <Auth />
    </div>
  );
}

export default App;
```

### 3.5 Ejecutar la aplicación

```bash
npm start
```

Navega a http://localhost:3000

---

## 📝 Paso 4: Probar el Flujo

1. **Clic en "Login con Keycloak"**
   - Serás redirigido a Keycloak
   
2. **Ingresar credenciales**:
   - Usuario: `testuser`
   - Contraseña: `password123`

3. **Observar los tokens**:
   - ID Token (información del usuario)
   - Access Token (para acceder a APIs)
   - Refresh Token (para renovar access token)

4. **Decodificar tokens en jwt.io**:
   - Copia el ID Token
   - Pégalo en https://jwt.io
   - Observa header, payload y signature

5. **Probar logout**:
   - Clic en "Logout"
   - Sesión cerrada en Keycloak

---

## 🔍 Verificación y Análisis

### Analizar ID Token

El ID Token debe contener claims como:
```json
{
  "exp": 1234567890,
  "iat": 1234567890,
  "auth_time": 1234567890,
  "jti": "...",
  "iss": "http://localhost:8080/realms/demo",
  "aud": "spa-client",
  "sub": "user-uuid",
  "typ": "ID",
  "azp": "spa-client",
  "session_state": "...",
  "email_verified": true,
  "name": "Test User",
  "preferred_username": "testuser",
  "given_name": "Test",
  "family_name": "User",
  "email": "test@example.com"
}
```

### Validar Firma

En jwt.io, en la sección "Verify Signature", pega la clave pública de Keycloak:

1. Ve a Keycloak Console
2. Realm `demo` → **Realm settings** → **Keys** → **RS256** → **Public key**
3. Copia la clave pública
4. Pégala en jwt.io

Si la firma es válida, verás ✅ "Signature Verified"

---

## ✅ Checklist de Completitud

- [ ] Keycloak corriendo en Docker
- [ ] Realm `demo` creado
- [ ] Client `spa-client` configurado
- [ ] Usuario `testuser` creado
- [ ] SPA funcionando en localhost:3000
- [ ] Login exitoso con PKCE
- [ ] Tokens recibidos y mostrados
- [ ] Tokens decodificados en jwt.io
- [ ] Firma validada correctamente
- [ ] Logout funcional

---

## 🐛 Troubleshooting

### Error: "Invalid redirect_uri"
- Verifica que en Keycloak el client tenga `http://localhost:3000/*` en Valid redirect URIs

### Error: "CORS error"
- Verifica Web origins en el client: `http://localhost:3000`

### Error: "Invalid code_verifier"
- Asegúrate de estar usando el mismo code_verifier que generaste

---

## 📚 Próximos Pasos

Una vez completado este lab:
- Procede al [Lab 2: API Gateway + JWT Validation](../lab-2-gateway-jwt-validation/)
- Experimenta agregando más scopes personalizados
- Prueba diferentes roles y observa cómo cambian los tokens

---

[⬅️ Volver a Labs](../README.md)

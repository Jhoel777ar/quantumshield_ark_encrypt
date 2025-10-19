# QuantumShield Ark Bidirectional

[![npm version](https://img.shields.io/npm/v/quantumshield_ark_bidirectional)](https://www.npmjs.com/package/quantumshield_ark_bidirectional)
[![TypeScript](https://img.shields.io/badge/TypeScript-Supported-blue)](https://www.typescriptlang.org/)
[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-green)](https://opensource.org/licenses/GPL-3.0)
[![WebAssembly](https://img.shields.io/badge/WebAssembly-Optimized-orange)](https://webassembly.org/)

**QuantumShield Ark Bidirectional** es una librería WebAssembly de alta seguridad para encriptación y desencriptación bidireccional. Diseñada para aplicaciones web que demandan criptografía robusta de grado militar.

---

## 🚀 Características

- **🔐 Dos Modos de Encriptación**: Modo Standard (rápido) y Modo PRO (máxima seguridad)
- **⚡ WebAssembly Optimizado**: Binario ligero (~400 KB) para máximo rendimiento
- **🔑 Arquitectura Zero-Knowledge**: El desarrollador NO conoce las claves generadas
- **🎯 API Simple**: Fácil integración en JavaScript/TypeScript
- **📦 Límite de Texto**: Hasta 10 MB por mensaje
- **🛡️ Protección de Datos**: Contra ataques de fuerza bruta, man-in-the-middle y tampering

---

## 📥 Instalación

```bash
npm i quantumshield_ark_bidirectional
```

---

## ⚠️ IMPORTANCIA: Configuración del Import de WASM

Para que la librería funcione correctamente, debes importar el archivo WASM de la siguiente manera:

```javascript
import wasmUrl from "quantumshield_ark_bidirectional/quantumshield_bg.wasm?url";
import init, { 
  wasm_generate_keypair, 
  wasm_encrypt, 
  wasm_encrypt_pro, 
  wasm_decrypt 
} from 'quantumshield_ark_bidirectional';

// Inicializar con la URL del WASM
await init(wasmUrl);
```

**Sin este import el paquete NO funcionará.**

---

## 💻 Uso

### API Disponible

- `wasm_generate_keypair()`: Genera un par de claves (X25519 + Ed25519)
- `wasm_encrypt(plaintext, x25519_pub, ed25519_sec, seed)`: Encripta en modo Standard
- `wasm_encrypt_pro(plaintext, x25519_pub, ed25519_sec, seed)`: Encripta en modo PRO
- `wasm_decrypt(ciphertext, x25519_sec, ed25519_pub)`: Desencripta automáticamente

---

## 📖 Ejemplo 1: Modo Standard (Rápido)

```javascript
import wasmUrl from "quantumshield_ark_bidirectional/quantumshield_bg.wasm?url";
import init, { wasm_generate_keypair, wasm_encrypt, wasm_decrypt } 
  from 'quantumshield_ark_bidirectional';

async function modoStandard() {
  // Inicializar WebAssembly
  await init(wasmUrl);

  // Generar claves
  const keypair = wasm_generate_keypair();
  const x25519_pub = new Uint8Array(keypair.x25519_pub);
  const x25519_sec = new Uint8Array(keypair.x25519_sec);
  const ed25519_pub = new Uint8Array(keypair.ed25519_pub);
  const ed25519_sec = new Uint8Array(keypair.ed25519_sec);

  // Encriptar
  const plaintext = "Mensaje confidencial";
  const seed = new TextEncoder().encode("session_123");
  const ciphertext = wasm_encrypt(plaintext, x25519_pub, ed25519_sec, seed);
  
  console.log("Modo: STANDARD");
  console.log("Ciphertext:", bytesToHex(ciphertext));

  // Desencriptar
  const decrypted = wasm_decrypt(ciphertext, x25519_sec, ed25519_pub);
  console.log("Decrypted:", decrypted);
}

function bytesToHex(bytes) {
  return Array.from(bytes).map(b => b.toString(16).padStart(2, '0')).join('');
}

modoStandard().catch(console.error);
```

---

## 📖 Ejemplo 2: Modo PRO (Grado Militar)

```javascript
import wasmUrl from "quantumshield_ark_bidirectional/quantumshield_bg.wasm?url";
import init, { wasm_generate_keypair, wasm_encrypt_pro, wasm_decrypt } 
  from 'quantumshield_ark_bidirectional';

async function modoPRO() {
  await init(wasmUrl);

  // Generar claves
  const keypair = wasm_generate_keypair();
  const x25519_pub = new Uint8Array(keypair.x25519_pub);
  const x25519_sec = new Uint8Array(keypair.x25519_sec);
  const ed25519_pub = new Uint8Array(keypair.ed25519_pub);
  const ed25519_sec = new Uint8Array(keypair.ed25519_sec);

  // Encriptar en modo PRO
  const plaintext = "Información clasificada";
  const seed = new TextEncoder().encode("secure_session_456");
  const ciphertext = wasm_encrypt_pro(plaintext, x25519_pub, ed25519_sec, seed);
  
  console.log("Modo: PRO");
  console.log("Ciphertext:", bytesToHex(ciphertext));

  // Desencriptar
  const decrypted = wasm_decrypt(ciphertext, x25519_sec, ed25519_pub);
  console.log("Decrypted:", decrypted);
}

function bytesToHex(bytes) {
  return Array.from(bytes).map(b => b.toString(16).padStart(2, '0')).join('');
}

modoPRO().catch(console.error);
```

---

## 🌐 Ejemplo 3: Aplicación Web Completa

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QuantumShield - Encriptación Web</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { 
      font-family: 'Segoe UI', sans-serif; 
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); 
      min-height: 100vh; 
      padding: 20px; 
    }
    .container { 
      max-width: 900px; 
      margin: 0 auto; 
      background: white; 
      border-radius: 15px; 
      box-shadow: 0 20px 60px rgba(0,0,0,0.3); 
      padding: 40px; 
    }
    h1 { color: #667eea; text-align: center; margin-bottom: 30px; }
    .mode-selector { 
      display: flex; 
      gap: 15px; 
      justify-content: center; 
      margin-bottom: 30px; 
    }
    .mode-btn { 
      padding: 12px 30px; 
      border: 2px solid #667eea; 
      background: white; 
      color: #667eea; 
      border-radius: 8px; 
      cursor: pointer; 
      font-weight: bold; 
      transition: all 0.3s; 
    }
    .mode-btn.active { background: #667eea; color: white; }
    .mode-btn:hover { transform: translateY(-2px); }
    .input-group { margin-bottom: 20px; }
    label { display: block; margin-bottom: 8px; color: #333; font-weight: 600; }
    input, textarea { 
      width: 100%; 
      padding: 12px; 
      border: 2px solid #e0e0e0; 
      border-radius: 8px; 
      font-size: 14px; 
    }
    textarea { min-height: 100px; resize: vertical; font-family: 'Courier New', monospace; }
    button { 
      width: 100%; 
      padding: 15px; 
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); 
      color: white; 
      border: none; 
      border-radius: 8px; 
      font-size: 16px; 
      font-weight: bold; 
      cursor: pointer; 
      transition: all 0.3s; 
      margin-top: 10px;
    }
    button:hover { transform: translateY(-2px); }
    .result { margin-top: 20px; padding: 20px; background: #f8f9fa; border-radius: 8px; }
    .key-display { 
      background: #f1f3f5; 
      padding: 10px; 
      border-radius: 5px; 
      font-family: 'Courier New', monospace; 
      font-size: 11px; 
      word-break: break-all; 
      margin: 8px 0; 
    }
    .section { margin-top: 30px; padding-top: 20px; border-top: 2px solid #e0e0e0; }
    .error { color: #f44336; }
    .success { color: #4caf50; }
    .loading { color: #667eea; font-style: italic; }
  </style>
</head>
<body>
  <div class="container">
    <h1>🔐 QuantumShield Ark Bidirectional</h1>

    <div class="mode-selector">
      <button class="mode-btn active" id="btnStandard">⚡ Standard</button>
      <button class="mode-btn" id="btnPro">🛡️ PRO</button>
    </div>

    <div class="section">
      <h3>Encriptar Mensaje</h3>
      <div class="input-group">
        <label>Mensaje a Encriptar:</label>
        <textarea id="plaintext" placeholder="Escribe tu mensaje aquí..."></textarea>
      </div>
      <button id="encryptBtn">🔒 Encriptar</button>
    </div>

    <div class="section">
      <h3>Desencriptar Mensaje</h3>
      <div class="input-group">
        <label>Ciphertext (hex):</label>
        <textarea id="ciphertext" placeholder="Pega el texto encriptado aquí"></textarea>
      </div>
      <div class="input-group">
        <label>X25519 Secret Key (hex):</label>
        <input type="text" id="x25519_sec" placeholder="Clave secreta X25519">
      </div>
      <div class="input-group">
        <label>Ed25519 Public Key (hex):</label>
        <input type="text" id="ed25519_pub" placeholder="Clave pública Ed25519">
      </div>
      <button id="decryptBtn">🔓 Desencriptar</button>
    </div>

    <div id="result" class="result" style="display: none;"></div>
  </div>

  <script type="module">
    import wasmUrl from "./node_modules/quantumshield_ark_bidirectional/quantumshield_bg.wasm?url";
    import init, { 
      wasm_generate_keypair, 
      wasm_encrypt, 
      wasm_encrypt_pro, 
      wasm_decrypt 
    } from './node_modules/quantumshield_ark_bidirectional/quantumshield.js';

    let currentMode = 'standard';
    let wasmReady = false;

    // Inicializar WASM
    init(wasmUrl).then(() => {
      wasmReady = true;
      console.log('WebAssembly initialized successfully');
    }).catch(err => {
      console.error('Error initializing WASM:', err);
      alert('Error initializing cryptography library. Please refresh the page.');
    });

    function hexEncode(bytes) {
      return Array.from(bytes).map(b => b.toString(16).padStart(2, '0')).join('');
    }

    function hexDecode(hex) {
      const bytes = new Uint8Array(hex.length / 2);
      for (let i = 0; i < hex.length; i += 2) {
        bytes[i / 2] = parseInt(hex.substr(i, 2), 16);
      }
      return bytes;
    }

    // Selector de modo
    document.getElementById('btnStandard').addEventListener('click', () => {
      currentMode = 'standard';
      document.getElementById('btnStandard').classList.add('active');
      document.getElementById('btnPro').classList.remove('active');
    });

    document.getElementById('btnPro').addEventListener('click', () => {
      currentMode = 'pro';
      document.getElementById('btnPro').classList.add('active');
      document.getElementById('btnStandard').classList.remove('active');
    });

    // Encriptar
    document.getElementById('encryptBtn').addEventListener('click', () => {
      if (!wasmReady) {
        alert('WASM not ready. Please wait...');
        return;
      }

      const plaintext = document.getElementById('plaintext').value;
      const resultDiv = document.getElementById('result');

      if (!plaintext) {
        resultDiv.innerHTML = '<p class="error">Por favor ingresa un mensaje</p>';
        resultDiv.style.display = 'block';
        return;
      }

      try {
        const keypair = wasm_generate_keypair();
        const x25519_pub = new Uint8Array(keypair.x25519_pub);
        const x25519_sec = new Uint8Array(keypair.x25519_sec);
        const ed25519_pub = new Uint8Array(keypair.ed25519_pub);
        const ed25519_sec = new Uint8Array(keypair.ed25519_sec);

        const startTime = performance.now();
        const ciphertext = currentMode === 'pro' 
          ? wasm_encrypt_pro(plaintext, x25519_pub, ed25519_sec, new TextEncoder().encode('seed'))
          : wasm_encrypt(plaintext, x25519_pub, ed25519_sec, new TextEncoder().encode('seed'));
        const endTime = performance.now();

        const modeName = currentMode === 'pro' ? 'PRO' : 'STANDARD';

        resultDiv.innerHTML = `
          <h4 class="success">✅ Encriptación Exitosa - ${modeName}</h4>
          <p><strong>Tiempo:</strong> ${(endTime - startTime).toFixed(2)} ms</p>
          <p><strong>Tamaño:</strong> ${plaintext.length} bytes → ${ciphertext.length} bytes</p>
          <p><strong>Ciphertext (hex):</strong></p>
          <div class="key-display">${hexEncode(ciphertext)}</div>
          <p><strong>Claves Generadas:</strong></p>
          <p>X25519 Public:</p>
          <div class="key-display">${hexEncode(x25519_pub)}</div>
          <p>X25519 Secret:</p>
          <div class="key-display">${hexEncode(x25519_sec)}</div>
          <p>Ed25519 Public:</p>
          <div class="key-display">${hexEncode(ed25519_pub)}</div>
          <p>Ed25519 Secret:</p>
          <div class="key-display">${hexEncode(ed25519_sec)}</div>
          <p class="error"><strong>⚠️ Guarda estas claves de forma segura</strong></p>
        `;
        resultDiv.style.display = 'block';
      } catch (e) {
        resultDiv.innerHTML = `<p class="error">Error: ${e.message}</p>`;
        resultDiv.style.display = 'block';
      }
    });

    // Desencriptar
    document.getElementById('decryptBtn').addEventListener('click', () => {
      if (!wasmReady) {
        alert('WASM not ready. Please wait...');
        return;
      }

      const ctHex = document.getElementById('ciphertext').value.trim();
      const x25519SecHex = document.getElementById('x25519_sec').value.trim();
      const ed25519PubHex = document.getElementById('ed25519_pub').value.trim();
      const resultDiv = document.getElementById('result');

      if (!ctHex || !x25519SecHex || !ed25519PubHex) {
        resultDiv.innerHTML = '<p class="error">Por favor completa todos los campos</p>';
        resultDiv.style.display = 'block';
        return;
      }

      try {
        const ciphertext = hexDecode(ctHex);
        const x25519_sec = hexDecode(x25519SecHex);
        const ed25519_pub = hexDecode(ed25519PubHex);

        const startTime = performance.now();
        const plaintext = wasm_decrypt(ciphertext, x25519_sec, ed25519_pub);
        const endTime = performance.now();

        resultDiv.innerHTML = `
          <h4 class="success">✅ Desencriptación Exitosa</h4>
          <p><strong>Tiempo:</strong> ${(endTime - startTime).toFixed(2)} ms</p>
          <p><strong>Mensaje:</strong></p>
          <div class="key-display" style="font-size: 14px; white-space: pre-wrap;">${plaintext}</div>
        `;
        resultDiv.style.display = 'block';
      } catch (e) {
        resultDiv.innerHTML = `<p class="error">Error: ${e.message}</p><p>Verifica que las claves sean correctas.</p>`;
        resultDiv.style.display = 'block';
      }
    });
  </script>
</body>
</html>
```

---

## 🔒 Comparación de Modos

| Aspecto | Standard | PRO |
|--------|----------|-----|
| Velocidad | Rápido | Más lento |
| Seguridad | Alta | Máxima |
| Capas de encriptación | Estándar | Reforzadas |
| Uso ideal | Mensajería general | Datos sensibles |

---

## 🛡️ Protección Proporcionada

La librería protege contra:

- Ataques de fuerza bruta mediante derivación de claves robusta
- Man-in-the-middle mediante intercambio de claves ECDH
- Falsificación mediante firmas digitales (Ed25519)
- Tampering mediante autenticación (Poly1305)
- Ataques de repetición mediante nonces únicos

---

## 📋 Requisitos

- Node.js v14 o superior
- Navegador moderno con WebAssembly:
  - Chrome 57+
  - Firefox 52+
  - Safari 11+
  - Edge 16+

---

## 🎯 Casos de Uso

**Modo Standard**: Mensajería, chat, transferencia de archivos, comunicaciones web general.

**Modo PRO**: Datos médicos, información financiera crítica, datos clasificados, almacenamiento de alto valor.

---

## 📊 Especificaciones

- Tamaño del binario: ~400 KB
- Límite de mensaje: 10 MB
- Curvas elípticas: X25519, Ed25519
- Cifrado AEAD: ChaCha20-Poly1305
- KDF: Argon2id
- Hash: SHA-512

---

## ⚠️ Advertencias Importantes

1. Verifica el import del WASM - es obligatorio para que funcione
2. Guarda las claves generadas de forma segura
3. Usa Modo PRO para datos altamente sensibles
4. Espera a que WASM se inicialice antes de usar las funciones
5. No modifiques el código fuente de la librería

---

## 📞 Soporte

Email: ark_dev@hotmail.com

---

## 🔒 Principios de Seguridad

### Zero-Knowledge Architecture

La librería implementa una arquitectura Zero-Knowledge que garantiza:

**El Desarrollador NO Conoce:**
- Las claves criptográficas generadas
- Los datos en texto plano una vez encriptados
- Las rutas de derivación de claves

**Terceros NO Pueden:**
- Reconstruir datos desde los hashes
- Acceder a información sensible sin las claves privadas
- Interceptar comunicaciones incluso en tránsito

**Sistema Completamente:**
- Verificable - Las firmas digitales garantizan autenticidad
- Aislado - Cada operación es independiente y segura

### Protecciones Implementadas

**Contra Ataques Comunes:**
- Fuerza bruta: Derivación de claves con Argon2id (resistente a memoria)
- Man-in-the-Middle: Intercambio de claves ECDH (X25519)
- Falsificación: Firmas digitales Ed25519
- Tampering: Autenticación ChaCha20-Poly1305
- Repetición: Nonces únicos por operación
- Canales laterales: Operaciones en tiempo constante

**Primitivas Criptográficas Utilizadas:**
- **X25519**: Curva elíptica para intercambio de claves seguro
- **Ed25519**: Firmas digitales deterministas y verificables
- **ChaCha20-Poly1305**: Cifrado AEAD de grado militar
- **Argon2id**: Derivación de claves resistente a GPU y ASIC
- **SHA-512**: Hash criptográfico de 512 bits
- **HMAC-SHA512**: Autenticación de mensajes (Modo PRO)

---

## ✅ Verificación de Integridad del Archivo WASM

Para garantizar la autenticidad y seguridad del paquete, verifica el hash SHA-256 del archivo WASM:

### Windows (PowerShell)
```powershell
Get-FileHash .\node_modules\quantumshield_ark_bidirectional\quantumshield_bg.wasm -Algorithm SHA256
```

### Linux/macOS
```bash
sha256sum ./node_modules/quantumshield_ark_bidirectional/quantumshield_bg.wasm
```

### Hash SHA-256 Esperado (Versión Actual)
```
78B0306BB1E4197CFC23BE862CBBE05EC7F3A7FE00B80214DA93EA442F039707
```

Si el hash no coincide, **no utilices el paquete**. Descarga nuevamente desde npm.

---

## 📜 Licencia

GPL-3.0

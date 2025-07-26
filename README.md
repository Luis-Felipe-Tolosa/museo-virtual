# 🏛️ MUSEO 3D - EXPLICACIÓN COMPLETA DEL CÓDIGO

## 📋 ÍNDICE
1. [Estructura General](#estructura-general)
2. [Tecnologías Utilizadas](#tecnologías-utilizadas)
3. [Inicialización del Sistema](#inicialización-del-sistema)
4. [Construcción del Entorno](#construcción-del-entorno)
5. [Sistema de Iluminación](#sistema-de-iluminación)
6. [Gestión de Obras de Arte](#gestión-de-obras-de-arte)
7. [Sistema de Controles](#sistema-de-controles)
8. [Interacciones del Usuario](#interacciones-del-usuario)
9. [Bucle de Animación](#bucle-de-animación)
10. [Funcionalidades Avanzadas](#funcionalidades-avanzadas)

---

## 🏗️ ESTRUCTURA GENERAL

### **HTML Base**
```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <!-- Metadatos y estilos CSS -->
  </head>
  <body>
    <div id="container">
      <!-- Elementos de interfaz de usuario -->
    </div>
    <script src="three.js"></script>
    <script>
      // Código JavaScript principal
    </script>
  </body>
</html>
```

**¿Por qué esta estructura?**
- **HTML semántico**: Organiza el contenido de manera lógica
- **CSS integrado**: Estilos embebidos para una sola aplicación
- **Three.js desde CDN**: Biblioteca 3D confiable y actualizada
- **JavaScript modular**: Funciones organizadas por responsabilidad

---

## 🛠️ TECNOLOGÍAS UTILIZADAS

### **1. Three.js (Biblioteca 3D)**
```javascript
// Componentes principales de Three.js
let scene;    // Escena 3D (contenedor de todos los objetos)
let camera;   // Cámara (punto de vista del usuario)
let renderer; // Renderizador (dibuja la escena en pantalla)
```

**Funciones clave:**
- **Scene**: Espacio 3D donde existen todos los objetos
- **Camera**: Define perspectiva y campo de visión
- **Renderer**: Convierte la escena 3D en píxeles 2D

### **2. WebGL + Canvas**
- **WebGL**: API gráfica de bajo nivel para 3D en navegadores
- **Canvas**: Elemento HTML donde se dibuja la escena
- **GPU Acceleration**: Aprovecha la tarjeta gráfica para renderizado

### **3. JavaScript ES6+**
- **Arrow functions**: Sintaxis moderna y concisa
- **Destructuring**: Extracción limpia de propiedades
- **Event Listeners**: Manejo de interacciones del usuario

---

## 🚀 INICIALIZACIÓN DEL SISTEMA

### **Función `init()`**
```javascript
function init() {
  // 1. Crear escena
  scene = new THREE.Scene();
  scene.fog = new THREE.Fog(0x222222, 0, 100);

  // 2. Configurar cámara
  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  camera.position.set(0, 5, 0);

  // 3. Crear renderizador
  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setClearColor(0x222222);
  renderer.shadowMap.enabled = true;

  // 4. Construir el museo
  createRoom();
  setupLighting();
  addMuseumDecorations();
  setupControls();
  setupEventListeners();

  // 5. Iniciar bucle de renderizado
  animate();
}
```

**Explicación detallada:**

1. **Escena (`THREE.Scene`)**:
   - Contenedor universal de todos los objetos 3D
   - `fog`: Niebla atmosférica para profundidad visual

2. **Cámara (`THREE.PerspectiveCamera`)**:
   - `75°`: Campo de visión (FOV) - qué tan amplio ve el usuario
   - `aspect ratio`: Relación ancho/alto de la pantalla
   - `0.1, 1000`: Planos de recorte cerca/lejos

3. **Renderizador (`THREE.WebGLRenderer`)**:
   - `antialias`: Suaviza bordes dentados
   - `shadowMap`: Habilita sombras realistas
   - `clearColor`: Color de fondo de la escena

---

## 🏢 CONSTRUCCIÓN DEL ENTORNO

### **Función `createRoom()`**
```javascript
function createRoom() {
  const roomSize = 45;      // Tamaño de la sala en unidades 3D
  const wallHeight = 16;    // Altura de las paredes

  // Materiales con texturas procedurales
  const floorMaterial = new THREE.MeshLambertMaterial({
    color: 0x888888,
    map: createFloorTexture()
  });

  // Geometrías básicas
  const floorGeometry = new THREE.PlaneGeometry(roomSize, roomSize);
  const floor = new THREE.Mesh(floorGeometry, floorMaterial);
  floor.rotation.x = -Math.PI / 2;  // Rotar 90° para que sea horizontal
  floor.receiveShadow = true;       // Recibe sombras de otros objetos
  scene.add(floor);

  // Crear 4 paredes usando BoxGeometry
  // ... código de paredes
}
```

**Conceptos clave:**

1. **Geometrías (`Geometry`)**:
   - `PlaneGeometry`: Superficies planas (suelo, techo)
   - `BoxGeometry`: Cajas 3D (paredes, marcos)
   - `CylinderGeometry`: Cilindros (columnas, pedestales)

2. **Materiales (`Material`)**:
   - `MeshLambertMaterial`: Superficie mate que responde a la luz
   - `color`: Color base del material
   - `map`: Textura aplicada al material

3. **Meshes (`Mesh`)**:
   - Combinación de geometría + material
   - Objeto final que se ve en la escena

### **Texturas Procedurales**
```javascript
function createFloorTexture() {
  const canvas = document.createElement('canvas');
  canvas.width = 512;
  canvas.height = 512;
  const context = canvas.getContext('2d');

  // Dibujar patrón de mármol
  context.fillStyle = '#f5f5f5';
  context.fillRect(0, 0, 512, 512);

  // Vetas de mármol con curvas Bézier
  for (let i = 0; i < 25; i++) {
    context.beginPath();
    context.moveTo(Math.random() * 512, Math.random() * 512);
    context.bezierCurveTo(/* puntos de control aleatorios */);
    context.stroke();
  }

  // Convertir canvas a textura Three.js
  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;  // Repetir en X
  texture.wrapT = THREE.RepeatWrapping;  // Repetir en Y
  texture.repeat.set(8, 8);              // 8x8 repeticiones
  return texture;
}
```

**¿Por qué texturas procedurales?**
- **Tamaño pequeño**: No requieren archivos de imagen externos
- **Calidad escalable**: Se generan en tiempo real
- **Personalización**: Totalmente controlables por código
- **Rendimiento**: Cargan instantáneamente

---

## 💡 SISTEMA DE ILUMINACIÓN

### **Función `setupLighting()`**
```javascript
function setupLighting() {
  // 1. Luz ambiental (iluminación general)
  const ambientLight = new THREE.AmbientLight(0x404040, 0.6);
  scene.add(ambientLight);

  // 2. Luz direccional principal (sol artificial)
  const mainLight = new THREE.DirectionalLight(0xffffff, 1.0);
  mainLight.position.set(0, 15, 0);
  mainLight.castShadow = true;
  mainLight.shadow.mapSize.width = 2048;   // Resolución de sombras
  mainLight.shadow.mapSize.height = 2048;
  scene.add(mainLight);

  // 3. Spotlights para cada área de cuadros
  const positions = [
    { x: 0, z: -20 },   // Norte
    { x: 20, z: 0 },    // Este
    { x: 0, z: 20 },    // Sur
    { x: -20, z: 0 }    // Oeste
  ];

  positions.forEach((pos) => {
    const spotlight = new THREE.SpotLight(0xffffff, 1.8, 35, Math.PI / 7, 0.3);
    spotlight.position.set(pos.x, 14, pos.z);
    spotlight.target.position.set(pos.x, 7, pos.z);
    spotlight.castShadow = true;
    scene.add(spotlight);
    scene.add(spotlight.target);
  });
}
```

**Tipos de luz explicados:**

1. **Ambient Light**:
   - Iluminación uniforme sin dirección
   - Simula luz rebotada del ambiente
   - Evita sombras completamente negras

2. **Directional Light**:
   - Luz paralela (como el sol)
   - Proyecta sombras consistentes
   - Iluminación principal de la escena

3. **Spot Light**:
   - Cono de luz direccional
   - Perfecta para resaltar obras de arte
   - Sombras suaves y realistas

**Configuración de sombras:**
```javascript
// En el renderizador
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;  // Sombras suaves

// En objetos que proyectan sombras
object.castShadow = true;

// En objetos que reciben sombras
object.receiveShadow = true;
```

---

## 🖼️ GESTIÓN DE OBRAS DE ARTE

### **Función Principal: `createArtwork()`**
```javascript
function createArtwork(imageUrl, position, title, description) {
  const loader = new THREE.TextureLoader();
  loader.crossOrigin = "anonymous";  // Para cargar imágenes externas

  loader.load(imageUrl, (texture) => {
    // 1. Obtener dimensiones originales
    const imageWidth = texture.image.width;
    const imageHeight = texture.image.height;

    // 2. Calcular dimensiones preservando aspecto
    const { width, height } = calculateArtworkDimensions(imageWidth, imageHeight);

    // 3. Crear marco dinámico
    const frameGroup = new THREE.Group();  // Contenedor de componentes

    // 4. Marco principal
    const frameGeometry = new THREE.BoxGeometry(width + 1, height + 1, 0.4);
    const frame = new THREE.Mesh(frameGeometry, frameMaterial);
    frameGroup.add(frame);

    // 5. Imagen con dimensiones correctas
    const paintingGeometry = new THREE.PlaneGeometry(width, height);
    const painting = new THREE.Mesh(paintingGeometry, new THREE.MeshLambertMaterial({ map: texture }));
    painting.position.z = 0.5;  // Adelante del marco
    frameGroup.add(painting);

    // 6. Placa informativa
    const plateTexture = createInfoPlate(title, description, imageWidth, imageHeight);
    // ... código de placa

    // 7. Agrupar y posicionar
    const artwork = new THREE.Group();
    artwork.add(frameGroup);
    artwork.position.set(position.x, position.y, position.z);
    artwork.rotation.y = position.angle || 0;
    artwork.userData = { title, description, type: "artwork" };

    scene.add(artwork);
    artworks.push(artwork);
  });
}
```

### **Cálculo de Dimensiones: `calculateArtworkDimensions()`**
```javascript
function calculateArtworkDimensions(imageWidth, imageHeight) {
  const maxWidth = 9;   // Límites del museo
  const maxHeight = 7;
  const aspectRatio = imageWidth / imageHeight;

  let width, height;

  if (aspectRatio >= 1) {  // Imagen horizontal
    width = Math.min(maxWidth, maxWidth);
    height = width / aspectRatio;

    if (height > maxHeight) {  // Si se pasa de altura
      height = maxHeight;
      width = height * aspectRatio;
    }
  } else {  // Imagen vertical
    height = Math.min(maxHeight, maxHeight);
    width = height * aspectRatio;

    if (width > maxWidth) {  // Si se pasa de ancho
      width = maxWidth;
      height = width / aspectRatio;
    }
  }

  return { width, height };
}
```

**¿Por qué este sistema?**
- **Preserva proporciones**: Las imágenes nunca se distorsionan
- **Optimiza espacio**: Usa el máximo espacio disponible
- **Flexibilidad**: Funciona con cualquier formato de imagen
- **Consistencia visual**: Mantiene la estética del museo

---

## 🎮 SISTEMA DE CONTROLES

### **Controles de Primera Persona**
```javascript
// Variables de estado de movimiento
let moveForward = false, moveBackward = false, moveLeft = false, moveRight = false;
let velocity = new THREE.Vector3();      // Velocidad actual
let direction = new THREE.Vector3();     // Dirección de movimiento
let yaw = 0, pitch = 0;                  // Rotación de cámara

// Control de teclado
function onKeyDown(event) {
  switch (event.code) {
    case 'KeyW': moveForward = true; break;
    case 'KeyS': moveBackward = true; break;
    case 'KeyA': moveLeft = true; break;
    case 'KeyD': moveRight = true; break;
  }
}

// Control de mouse (Pointer Lock API)
function onMouseMove(event) {
  if (!isPointerLocked) return;

  const sensitivity = 0.002;
  yaw -= event.movementX * sensitivity;    // Rotación horizontal
  pitch -= event.movementY * sensitivity;  // Rotación vertical

  // Limitar pitch (no dar volteretas)
  pitch = Math.max(-Math.PI/2 + 0.1, Math.min(Math.PI/2 - 0.1, pitch));

  // Aplicar rotación a cámara
  camera.rotation.order = 'YXZ';
  camera.rotation.y = yaw;
  camera.rotation.x = pitch;
}
```

### **Pointer Lock API**
```javascript
function setupControls() {
  renderer.domElement.addEventListener('click', () => {
    renderer.domElement.requestPointerLock();  // Capturar mouse
  });

  document.addEventListener('pointerlockchange', () => {
    if (document.pointerLockElement === renderer.domElement) {
      isPointerLocked = true;
      document.addEventListener('mousemove', onMouseMove);
    } else {
      isPointerLocked = false;
      document.removeEventListener('mousemove', onMouseMove);
    }
  });
}
```

**¿Por qué Pointer Lock?**
- **Inmersión total**: El cursor desaparece
- **Rotación ilimitada**: Sin límites de pantalla
- **Experiencia de videojuego**: Controles intuitivos
- **Seguridad**: El usuario debe hacer clic para activar

---

## 🔍 INTERACCIONES DEL USUARIO

### **Detección de Obras (Raycasting)**
```javascript
function checkArtworkLookAt() {
  if (!isPointerLocked) return;

  // 1. Crear rayo desde el centro de la cámara
  raycaster.setFromCamera(new THREE.Vector2(0, 0), camera);

  // 2. Detectar intersecciones con obras de arte
  const intersects = raycaster.intersectObjects(artworks, true);

  if (intersects.length > 0) {
    // 3. Obtener la obra más cercana
    const artwork = intersects[0].object.parent.parent || intersects[0].object.parent;

    if (artwork.userData.type === 'artwork' && artwork !== currentArtwork) {
      // 4. Mostrar información
      showArtworkInfo(artwork.userData.title, artwork.userData.description);
      currentArtwork = artwork;
    }
  } else if (currentArtwork) {
    // 5. Ocultar información si no mira ninguna obra
    hideArtworkInfo();
    currentArtwork = null;
  }
}
```

**Conceptos de Raycasting:**
- **Ray**: Línea infinita desde un punto en una dirección
- **Intersection**: Punto donde el rayo toca un objeto
- **Distance**: Distancia al punto de intersección
- **Object**: Referencia al objeto intersectado

### **Carga de Imágenes del Usuario**
```javascript
function handleImageUpload() {
  const fileInput = document.getElementById('file-input');
  const files = fileInput.files;

  if (files.length === 0) {
    alert('Por favor selecciona al menos una imagen');
    return;
  }

  const radius = 20;  // Radio de colocación

  Array.from(files).forEach((file, index) => {
    const reader = new FileReader();
    
    reader.onload = (e) => {
      // Calcular posición circular
      const totalArtworks = artworks.length + index;
      const angleStep = (2 * Math.PI) / Math.max(8, totalArtworks + 1);
      const angle = angleStep * totalArtworks;
      const x = Math.sin(angle) * radius;
      const z = Math.cos(angle) * radius;

      // Crear obra de arte
      createArtwork(
        e.target.result,                    // Data URL de la imagen
        { x, y: 7, z, angle: angle + Math.PI },  // Posición y rotación
        file.name.split('.')[0],            // Título (nombre sin extensión)
        'Obra subida por el usuario'        // Descripción
      );
    };

    reader.readAsDataURL(file);  // Convertir archivo a Data URL
  });
}
```

**FileReader API:**
- **readAsDataURL()**: Convierte archivo a URL base64
- **onload**: Evento cuando la carga termina
- **result**: Datos del archivo como string

---

## 🔄 BUCLE DE ANIMACIÓN

### **Función `animate()`**
```javascript
function animate() {
  requestAnimationFrame(animate);  // Programar siguiente frame

  if (isPointerLocked) {
    const time = performance.now();
    const delta = (time - prevTime) / 1000;  // Tiempo entre frames

    // 1. Aplicar fricción (desaceleración gradual)
    velocity.x -= velocity.x * 8.0 * delta;
    velocity.z -= velocity.z * 8.0 * delta;

    // 2. Calcular dirección de movimiento
    direction.z = Number(moveForward) - Number(moveBackward);
    direction.x = Number(moveRight) - Number(moveLeft);
    direction.normalize();  // Vector unitario

    // 3. Aplicar movimiento relativo a la rotación de cámara
    if (moveForward || moveBackward) {
      velocity.x -= Math.sin(camera.rotation.y) * direction.z * 50.0 * delta;
      velocity.z -= Math.cos(camera.rotation.y) * direction.z * 50.0 * delta;
    }
    if (moveLeft || moveRight) {
      velocity.x += Math.sin(camera.rotation.y + Math.PI/2) * direction.x * 50.0 * delta;
      velocity.z += Math.cos(camera.rotation.y + Math.PI/2) * direction.x * 50.0 * delta;
    }

    // 4. Actualizar posición de cámara
    camera.position.x += velocity.x * delta;
    camera.position.z += velocity.z * delta;
    camera.position.y = 5;  // Altura fija

    // 5. Limitar movimiento dentro de la sala
    const roomLimit = 20;
    camera.position.x = Math.max(-roomLimit, Math.min(roomLimit, camera.position.x));
    camera.position.z = Math.max(-roomLimit, Math.min(roomLimit, camera.position.z));

    prevTime = time;
  }

  // 6. Renderizar la escena
  renderer.render(scene, camera);
}
```

**Conceptos clave:**

1. **requestAnimationFrame()**:
   - Sincroniza con la tasa de refresco del monitor
   - Optimiza rendimiento (60 FPS típico)
   - Se pausa cuando la pestaña no está visible

2. **Delta Time**:
   - Tiempo transcurrido entre frames
   - Hace que el movimiento sea independiente del framerate
   - Movimiento consistente en diferentes dispositivos

3. **Fricción**:
   - Desaceleración gradual cuando se suelta una tecla
   - Movimiento más natural y realista
   - Evita paradas bruscas

---

## 🚀 FUNCIONALIDADES AVANZADAS

### **1. Decoraciones del Museo**
```javascript
function addMuseumDecorations() {
  addPedestals();    // Pedestales con esculturas
  addPlants();       // Plantas decorativas
  addBenches();      // Bancos para descansar
  addMuseumSigns();  // Señalización
  addColumns();      // Columnas arquitectónicas
  addSculptures();   // Esculturas geométricas
}
```

**Cada decoración incluye:**
- **Geometrías complejas**: Combinación de formas primitivas
- **Materiales específicos**: Colores y texturas apropiadas
- **Posicionamiento estratégico**: Distribución estética
- **Sombras**: Proyección y recepción de sombras

### **2. Texturas Procedurales Avanzadas**
```javascript
// Ejemplo: Textura de mármol para el suelo
function createFloorTexture() {
  const canvas = document.createElement('canvas');
  const context = canvas.getContext('2d');

  // Base sólida
  context.fillStyle = '#f5f5f5';
  context.fillRect(0, 0, 512, 512);

  // Vetas orgánicas con curvas Bézier
  context.strokeStyle = '#e0e0e0';
  for (let i = 0; i < 25; i++) {
    context.beginPath();
    context.moveTo(Math.random() * 512, Math.random() * 512);
    context.bezierCurveTo(
      Math.random() * 512, Math.random() * 512,  // Control 1
      Math.random() * 512, Math.random() * 512,  // Control 2
      Math.random() * 512, Math.random() * 512   // Final
    );
    context.stroke();
  }

  return new THREE.CanvasTexture(canvas);
}
```

### **3. Sistema de Información Dinámico**
```javascript
function showArtworkInfo(title, description, dimensions, displaySize) {
  const info = document.getElementById('artwork-info');
  
  // Actualizar contenido
  document.getElementById('artwork-title').textContent = title;
  document.getElementById('artwork-description').textContent = description;
  
  // Información técnica
  let details = 'Colección Personal • 2024 • Arte Digital';
  if (dimensions) details += ` • ${dimensions}`;
  if (displaySize) details += ` • Mostrado: ${displaySize}`;
  
  document.getElementById('artwork-details').textContent = details;
  
  // Mostrar con transición CSS
  info.classList.add('visible');
}
```

---

## 📊 RENDIMIENTO Y OPTIMIZACIÓN

### **Técnicas Implementadas:**

1. **Culling Automático**:
   - Three.js no renderiza objetos fuera del campo de visión
   - Mejora significativa del rendimiento

2. **Reutilización de Geometrías**:
   ```javascript
   const sharedGeometry = new THREE.BoxGeometry(1, 1, 1);
   // Usar la misma geometría para múltiples objetos
   ```

3. **Texturas Optimizadas**:
   - Tamaños de potencia de 2 (512x512, 1024x1024)
   - Compresión automática del navegador

4. **Shadow Map Configurado**:
   ```javascript
   renderer.shadowMap.enabled = true;
   renderer.shadowMap.type = THREE.PCFSoftShadowMap;  // Calidad vs rendimiento
   ```

5. **Límites de Movimiento**:
   - Evita renderizar objetos infinitamente lejanos
   - Mantiene al usuario dentro del área optimizada

---

## 🎯 PUNTOS CLAVE 

### **Arquitectura del Software:**
1. **Modularidad**: Cada función tiene una responsabilidad específica
2. **Escalabilidad**: Fácil agregar nuevas funcionalidades
3. **Mantenibilidad**: Código legible y bien documentado
4. **Rendimiento**: Optimizado para ejecutar suavemente

### **Tecnologías Justificadas:**
1. **Three.js**: Estándar de facto para 3D en web
2. **WebGL**: Aceleración por hardware
3. **Canvas API**: Texturas procedurales eficientes
4. **File API**: Carga de archivos del usuario

### **Funcionalidades Destacadas:**
1. **Preservación de aspecto**: Innovación técnica importante
2. **Controles inmersivos**: Experiencia de usuario superior
3. **Carga dinámica**: Funcionalidad práctica y útil
4. **Ambiente realista**: Atención al detalle visual

### **Casos de Uso:**
1. **Galerías personales**: Mostrar colecciones privadas
2. **Exhibiciones virtuales**: Eventos durante pandemia
3. **Portafolios artísticos**: Presentación profesional
4. **Educación**: Museos educativos interactivos

---

## 📝 CONCLUSIONES

Este proyecto demuestra:

- **Integración compleja** de múltiples tecnologías web
- **Solución de problemas reales** (preservación de aspecto)
- **Experiencia de usuario inmersiva** con controles intuitivos
- **Código production-ready** con optimizaciones de rendimiento
- **Arquitectura escalable** para futuras mejoras

**El resultado es una aplicación web 3D completamente funcional que rivaliza con software de escritorio especializado, ejecutándose directamente en el navegador sin instalaciones adicionales.**

# 🏖️ Vacation Comparator — Plan de Proyecto

> **App web HTML para comparar hospedajes vacacionales usando IA**

---

## 🎯 Objetivo

Crear una aplicación web single-file (HTML + JS + CSS) que permita al usuario:

1. Gestionar **múltiples viajes** (ej: "Viaje a Bariloche 2026", "Semana Santa Mar del Plata").
2. Dentro de cada viaje, **pegar mails o mensajes completos** (cualquier formato) de respuestas de alojamientos.
3. La app usa **IA gratuita** para desmenuzar y clasificar automáticamente cada mensaje.
4. Presenta un **comparador visual premium** con tarjetas por alojamiento, mostrando precio, características, ubicación, fotos, etc.

---

## 👤 Flujo del Operario (UX ideal)

```
1. Abre la app → ve sus viajes guardados (o crea uno nuevo)
2. Selecciona un viaje
3. Hace clic en "➕ Agregar Alojamiento"
4. Aparece un diálogo con un textarea grande
5. Pega el mail/mensaje completo del hospedaje (cualquier formato)
6. La app envía el texto a la IA → extrae los datos estructurados
7. Aparece la tarjeta del alojamiento en el comparador
8. Repite para cada hospedaje
9. Compara todos en una vista de cuadros lado a lado
```

---

## 🏗️ Arquitectura

### Stack
- **HTML5** — estructura semántica
- **CSS3 Vanilla** — diseño premium (dark mode, glassmorphism, animaciones)
- **JavaScript ES6+** — lógica, llamadas a IA, localStorage
- **Sin backend** — todo client-side, sin instalación

### Persistencia
- `localStorage` para guardar viajes, alojamientos y datos extraídos
- Exportación a JSON (backup manual)

---

## 🤖 Integración con IA Gratuita

### Opción primaria: **Google Gemini API (free tier)**
- Modelo: `gemini-2.0-flash` (gratuito, 1500 req/día)
- El usuario ingresa su API Key (se guarda en localStorage)
- Endpoint REST directo desde el browser

### Opción secundaria: **OpenRouter.ai (free models)**
- Modelos free: `mistralai/mistral-7b-instruct`, `meta-llama/llama-3-8b-instruct`
- Fallback si no hay Gemini key

### Opción terciaria: **Hugging Face Inference API (free)**
- Sin necesidad de tarjeta de crédito

> **Estrategia**: La app pregunta qué IA usar en la configuración inicial y permite cambiar en cualquier momento.

### Prompt de extracción (template)
La IA recibe el texto del mail y debe retornar un JSON estructurado:

```json
{
  "nombre": "Cabaña Los Pinos",
  "tipo": "cabaña | hotel | departamento | hostel | ...",
  "precio_por_noche": 85.00,
  "moneda": "USD | ARS | EUR",
  "precio_total": 510.00,
  "fechas_disponibles": "del 15 al 21 de enero",
  "capacidad": "4 personas",
  "habitaciones": 2,
  "banos": 1,
  "caracteristicas": ["WiFi", "Pileta", "Estacionamiento", "Aire acondicionado", "Parrilla", "Mascotas permitidas"],
  "ubicacion": "San Carlos de Bariloche, a 3km del centro",
  "coordenadas": null,
  "politica_cancelacion": "Sin cargos hasta 7 días antes",
  "forma_pago": "Transferencia bancaria, efectivo",
  "contacto": "cabañaslospinosbrc@gmail.com / +54 9 294 4123456",
  "fotos_urls": [],
  "notas_adicionales": "Incluye desayuno los primeros 2 días",
  "calificacion_ia": 8.5,
  "razon_calificacion": "Buena relación precio-calidad, pileta, acepta mascotas",
  "datos_faltantes": ["check-in/check-out", "fotos"],
  "texto_original_resumido": "Resumen en 2-3 oraciones del mensaje recibido"
}
```

---

## 📐 Pantallas / Vistas

### 1. Home — Mis Viajes
- Lista de viajes creados (cards con imagen de destino, fechas, cantidad de hospedajes)
- Botón "Nuevo Viaje"
- Barra de configuración (API key, tema)

### 2. Vista de Viaje — Comparador
- Header con nombre del viaje y fechas
- **Vista Grilla**: Cards de hospedajes lado a lado
- **Vista Tabla**: Comparación fila por fila de características
- Botón "Agregar Hospedaje" → abre modal
- Filtros/orden: por precio, por calificación IA, por características

### 3. Modal "Pegar Mail"
- Textarea grande (full height en mobile)
- Botón "Analizar con IA" → spinner de carga
- Preview del JSON extraído antes de confirmar
- Botón "Confirmar y Agregar"

### 4. Tarjeta de Alojamiento
- Nombre + tipo + badge de calificación IA
- 📍 Ubicación
- 💰 Precio por noche + precio total estimado
- 🛏️ Habitaciones / Baños / Capacidad
- ✅ Lista de características (iconos)
- 📋 Política de cancelación
- 💳 Formas de pago
- 📷 Galería de fotos (si hay URLs en el mail)
- 🗒️ Notas adicionales
- ⚠️ Datos faltantes (en rojo, para hacer seguimiento)
- Botones: Editar | Eliminar | Ver mail original

### 5. Vista Tabla Comparativa
- Filas: nombre del atributo
- Columnas: cada alojamiento
- Resalta la mejor opción por fila (precio más bajo, más características, etc.)

---

## 💎 Características Premium de Diseño

- **Dark mode** por defecto con opción light
- **Glassmorphism** en cards y modales
- **Gradientes** violeta/azul/cyan como paleta principal
- **Micro-animaciones**: entrada de cards con fade+slide, hover effects
- **Responsive**: mobile-first, funciona perfecto en celular
- **Tipografía**: Inter o Outfit (Google Fonts)
- **Iconos**: Emojis nativos + posiblemente Lucide Icons (CDN)
- **Skeleton loaders** mientras la IA procesa

---

## 📦 Estructura de Archivos

```
vacation-comparator/
├── PLAN.md              ← este archivo
├── index.html           ← app completa (single file posible)
├── style.css            ← estilos (si se separa)
└── app.js               ← lógica JS (si se separa)
```

> Se puede hacer single-file (`index.html` con todo embebido) para máxima portabilidad.

---

## 🗃️ Modelo de Datos (localStorage)

```javascript
// Estructura guardada en localStorage
{
  "config": {
    "ia_provider": "gemini",   // gemini | openrouter | huggingface
    "api_key": "...",
    "tema": "dark"
  },
  "viajes": [
    {
      "id": "viaje_001",
      "nombre": "Bariloche Enero 2027",
      "destino": "Bariloche, Argentina",
      "fecha_desde": "2027-01-15",
      "fecha_hasta": "2027-01-22",
      "creado": "2026-08-12T09:00:00",
      "hospedajes": [
        {
          "id": "hosp_001",
          "fecha_agregado": "2026-08-12T09:05:00",
          "mail_original": "Hola! Te informamos que...",
          "datos": {},
          "editado_manualmente": false
        }
      ]
    }
  ]
}
```

---

## 🚀 Fases de Desarrollo

### Fase 1 — MVP
- [ ] Estructura HTML base con diseño premium
- [ ] Gestión de viajes (CRUD) con localStorage
- [ ] Modal de pegado de mail
- [ ] Integración con Gemini API (prompt de extracción)
- [ ] Tarjetas de hospedaje con datos extraídos
- [ ] Vista grilla del comparador

### Fase 2 — Comparador Avanzado
- [ ] Vista tabla comparativa con highlights
- [ ] Filtros y ordenamiento
- [ ] Edición manual de datos extraídos
- [ ] Ver mail original desde la tarjeta

### Fase 3 — Polish
- [ ] Galería de fotos (drag & drop de imágenes locales)
- [ ] Exportar comparación como PDF o imagen
- [ ] Fallback a OpenRouter si falla Gemini
- [ ] Modo offline (usar datos cacheados)
- [ ] Compartir viaje como link (encoding en URL)

---

## ⚠️ Consideraciones Técnicas

### Variabilidad de formatos de mail
Los mails pueden venir en formatos muy distintos:
- Texto corrido en párrafos
- Tablas HTML copiadas
- Listas de características
- Mensajes muy cortos ("Te ofrezco la cabaña X a $Y la noche")
- Mails con emojis, mayúsculas, ortografía informal

**Solución**: El prompt de IA es muy flexible y pide extraer "lo que puedas encontrar",
dejando null en lo que no aparezca, y señalando en datos_faltantes qué no estaba en el mensaje.

### API Key del usuario
- Se pide en el primer uso con un modal de bienvenida
- Se guarda en `localStorage` (no sale del browser del usuario)
- Se puede cambiar/borrar desde Configuración

### Límites de la API gratuita
- Gemini Free: 1500 requests/día, suficiente para uso personal
- Se muestra un contador de uso estimado

### CORS
- Las APIs de Gemini y OpenRouter permiten llamadas desde browser directamente (no se necesita proxy)

---

## 📝 Notas Finales

- La app debe funcionar abriendo index.html directamente en el browser (sin servidor)
- Los datos nunca salen del dispositivo del usuario (excepto el texto del mail que se envía a la IA)
- Advertir al usuario sobre privacidad dentro de la app
- Hacer el prompt de extracción robusto con ejemplos few-shot para mejor precisión

---

*Plan creado: 2026-08-12*
*Próximo paso: Aprobación del usuario → comenzar Fase 1*

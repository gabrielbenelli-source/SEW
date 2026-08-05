# 🪡 Atelier & Reparación de Prendas - App Web

Aplicación web interactiva desarrollada para la gestión, catálogo de precios y agendamiento de servicios de modistería, compostura y ajuste de prendas. Cuenta con un flujo intuitivo para clientes (catálogo por categorías, carrito interactivo y coordinación por WhatsApp) y un panel administrativo integrado con control de acceso por clave para la edición de precios en tiempo real.

---

## 🚀 Características Principales

- ** Catálogo de Servicios Categorizado:** Explora más de 90 servicios de costura organizados por prendas (Camisas, Pantalones, Vestidos de Gala, Ropa Deportiva, Institucional, etc.).
- ** Temas Visuales Dinámicos:** Conmutador de estilo integrado (**Blanco & Plata** y **Negro & Dorado**).
- **🛒 Carrito y Cotizador en Tiempo Real:** Selección dinámica de servicios con cálculo automático de totales.
- **🛵 Flujo de Checkout Adaptativo:** Permite seleccionar tipo de entrega (Domicilio o Tienda) y modalidad de atención (Medición en casa o Asesoría presencial).
- **📲 Integración Directa con WhatsApp:** Genera un mensaje formateado con el desglose del pedido para agendar de inmediato.
- **⚙️ Panel de Administración Integrado (`AdminModal`):** 
  - Autenticación mediante contraseña maestra (`19831984`).
  - Edición de precios en tiempo real con persistencia en `localStorage`.
  - Agregado de nuevos servicios personalizados y eliminación de ítems.
  - Buscador interno de servicios.

---

## 🛠️ Tecnologías Utilizadas

- **Framework:** Vue 3 (Composition API / `<script setup lang="ts">`)
- **Lenguaje:** TypeScript
- **Bundler / Dev Server:** Vite
- **Estilos:** CSS3 / SASS (Modular con variables de tema)
- **Persistencia de Datos Local:** LocalStorage Web API

---

## 📋 Requisitos Previos

Asegúrate de tener instalado en tu equipo:

- **Node.js**: versión 18.0 o superior
- **npm**: versión 9.0 o superior (o bien `pnpm` / `yarn`)

---

## 🔧 Instalación y Configuración

1. **Clonar el repositorio o situarse en el directorio del proyecto:**
   ```bash
   cd design-app/vue-project
<!-- src/App.vue -->
<template>
  <div class="app-container" :class="currentTheme">
    <!-- BANNER DE PRESENTACIÓN AUDIOVISUAL (ESTÁTICO EN LA PARTE SUPERIOR) -->
    <div class="theme-switcher-bar">
      <span class="theme-label">Presentación:</span>
      <div class="theme-toggle-group">
        <button 
          class="btn-theme theme-silver" 
          :class="{ active: currentTheme === 'theme-silver' }"
          @click="currentTheme = 'theme-silver'"
        >
          <span class="dot silver-dot"></span> Blanco & Plata
        </button>
        <button 
          class="btn-theme theme-gold" 
          :class="{ active: currentTheme === 'theme-gold' }"
          @click="currentTheme = 'theme-gold'"
        >
          <span class="dot gold-dot"></span> Negro & Dorado
        </button>
      </div>
    </div>

    <!-- PANTALLA 1: CATÁLOGO -->
    <div v-if="currentStep === 'catalog'" class="step-container">
      <header class="catalog-header">
        <div class="brand-badge">Sustentable & Local</div>
        <h2 class="metallic-title">Reparación & Calce Perfecto</h2>
        <p class="subtitle">Dale una segunda vida a tus prendas favoritas</p>
      </header>

      <!-- SECCIÓN ¿CÓMO FUNCIONA? -->
      <section class="visual-procedure">
        <h3 class="section-title">Tu servicio en 4 simples pasos</h3>
        <div class="procedure-grid">
          <div class="procedure-card">
            <div class="proc-image-wrapper">
              <img src="https://images.unsplash.com/photo-1528459801416-a9e53bbf4e17?auto=format&fit=crop&w=200&q=80" alt="Marcar prenda" />
              <span class="step-number">1</span>
            </div>
            <h4>1. Selecciona</h4>
            <p>Elige el servicio. Si es directo, marca el doblez en casa con un alfiler.</p>
          </div>
          <div class="procedure-card">
            <div class="proc-image-wrapper">
              <img src="https://images.unsplash.com/photo-1534641289555-15f81c8c2585?auto=format&fit=crop&w=200&q=80" alt="Retiro a domicilio" />
              <span class="step-number">2</span>
            </div>
            <h4>2. Retiramos</h4>
            <p>Buscamos la prenda en tu domicilio o la dejas en nuestro punto.</p>
          </div>

          <div class="procedure-card">
            <div class="proc-image-wrapper">
              <img src="https://images.unsplash.com/photo-1552374196-1ab2a1c593e8?auto=format&fit=crop&w=200&q=80" alt="Modista trabajando" />
              <span class="step-number">3</span>
            </div>
            <h4>3. Taller</h4>
            <p>Modistas expertas procesan tu requerimiento con terminaciones de boutique.</p>
          </div>

          <div class="procedure-card">
            <div class="proc-image-wrapper">
              <img src="https://images.unsplash.com/photo-1489987707025-afc232f7ea0f?auto=format&fit=crop&w=200&q=80" alt="Prenda devuelta" />
              <span class="step-number">4</span>
            </div>
            <h4>4. Recibe</h4>
            <p>Te devolvemos tu prenda lista para usar en perfectas condiciones.</p>
          </div>
        </div>
      </section>

      <hr class="section-divider" />

      <!-- PESTAÑAS DE CATEGORÍAS ROBUSTAS -->
      <div class="filter-bar">
        <button 
          v-for="cat in categories" 
          :key="cat" 
          :class="{ active: selectedCategory === cat }"
          @click="selectedCategory = cat"
        >
          {{ cat }}
        </button>
      </div>

      <!-- LISTA DE TARJETAS DE SERVICIO -->
      <div class="services-grid">
        <div 
          v-for="service in filteredServices" 
          :key="service.id" 
          class="service-card"
        >
          <div class="service-meta">
            <span class="service-tag">{{ service.tag }}</span>
            <h4>{{ service.name }}</h4>
            <p class="service-desc" v-if="service.description">{{ service.description }}</p>
            <p class="price">Desde ${{ service.price.toLocaleString('es-CL') }}</p>
          </div>
          <button 
            class="btn-add" 
            :class="{ added: isAdded(service.id) }"
            @click="toggleCart(service)"
          >
            {{ isAdded(service.id) ? '✓ Añadido' : '+ Agregar' }}
          </button>
        </div>
        <div v-if="filteredServices.length === 0" class="empty-state">
          No hay servicios cargados en esta pestaña.
        </div>
      </div>

      <!-- SECCIÓN: GARANTÍA DE CALCE PERFECTO -->
      <section class="guarantee-section">
        <div class="guarantee-badge">🪡 Compromiso de Atelier</div>
        <h3>Garantía de Calce Perfecto</h3>
        <p>
          Nuestra prioridad es que tu ropa te quede impecable. Si el ajuste no queda exactamente como lo imaginaste en la primera entrega, <strong>lo reajustamos sin costo adicional</strong> dentro de los primeros 10 días. Tu satisfacción y la segunda vida de tu prenda están completamente aseguradas.
        </p>
      </section>

      <!-- SECCIÓN: PREGUNTAS FRECUENTES (FAQ) -->
      <section class="faq-section">
        <h3 class="section-title">Preguntas Frecuentes</h3>
        <div class="faq-container">
          <div 
            v-for="(faq, index) in faqs" 
            :key="index" 
            class="faq-item"
            :class="{ open: faq.isOpen }"
            @click="toggleFaq(index)"
          >
            <div class="faq-question">
              <h4>{{ faq.question }}</h4>
              <span class="faq-arrow">{{ faq.isOpen ? '▲' : '▼' }}</span>
            </div>
            <div v-if="faq.isOpen" class="faq-answer">
              <p>{{ faq.answer }}</p>
            </div>
          </div>
        </div>
      </section>

      <!-- BARRA FLOTANTE DE CARRITO ACTIVO -->
      <div v-if="cart.length > 0" class="floating-cart-bar">
        <div class="cart-info">
          <span>{{ cart.length }} {{ cart.length === 1 ? 'servicio' : 'servicios' }}</span>
          <strong>Total: ${{ cartTotal.toLocaleString('es-CL') }}</strong>
        </div>
        <button class="btn-continue" @click="currentStep = 'checkout'">
          Configurar Entrega ➔
        </button>
      </div>
    </div>

    <!-- PANTALLA 2: CHECKOUT & CIERRE -->
    <div v-else-if="currentStep === 'checkout'" class="step-container checkout-view">
      <button class="btn-back" @click="currentStep = 'catalog'">⬅ Volver al Catálogo</button>
      <h2>Resumen de tu Orden</h2>
      <div class="cart-summary">
        <div v-for="item in cart" :key="item.id" class="cart-item">
          <div>
            <span class="item-title">{{ item.name }}</span>
            <span class="item-tag-sub">{{ item.tag }}</span>
          </div>
          <strong>${{ item.price.toLocaleString('es-CL') }}</strong>
        </div>
        <div class="cart-total-row">
          <span>Total Estimado Base:</span>
          <strong>${{ cartTotal.toLocaleString('es-CL') }}</strong>
        </div>
      </div>
      <hr class="section-divider" />
      <div class="form-section">
        <h3>1. Método de Entrega</h3>
        <div class="options-grid">
          <button :class="{ active: deliveryMethod === 'domicilio' }" @click="deliveryMethod = 'domicilio'">🛵 Retiro a Domicilio</button>
          <button :class="{ active: deliveryMethod === 'tienda' }" @click="deliveryMethod = 'tienda'">🏪 Dejar en Tienda</button>
        </div>
      </div>
      <div class="form-section">
        <h3>2. Tipo de Atención</h3>
        <div class="options-grid vertical">
          <button :class="{ active: serviceType === 'estandar' }" @click="serviceType = 'estandar'">⚡ Envío Directo (Prenda ya marcada por mí en casa)</button>
          <button :class="{ active: serviceType === 'presencial' }" @click="serviceType = 'presencial'">🪡 Necesito Asesoría Presencial de la Modista</button>
        </div>
      </div>
      <button @click="sendToWhatsApp" class="btn-whatsapp">
        💬 Enviar Pedido a WhatsApp y Coordinar
      </button>
    </div>

    <!-- BOTÓN FLOTANTE DE ACCESO ADMINISTRATIVO -->
    <button class="btn-admin-trigger" @click="isAdminOpen = true" title="Panel de Administración">
      ⚙️ Admin
    </button>

    <!-- COMPONENTE MODAL DE ADMINISTRACIÓN -->
    <AdminModal 
      :isOpen="isAdminOpen" 
      @close="isAdminOpen = false" 
      @update-services="syncServicesFromStorage"
    />

    <!-- CIRCUNFERENCIA DE CONTACTO HUMANO -->
    <div class="whatsapp-wrapper" :class="{ 'with-cart': cart.length > 0 && currentStep === 'catalog' }">
      <span class="whatsapp-label">¿Necesitas ayuda? Escríbenos aquí</span>
      <a 
        :href="'https://api.whatsapp.com/send?phone=' + phoneNumber + '&text=Hola!%20Me%20gustar%C3%ADa%20recibir%20ayuda%20para%20coordinar%20la%20reparaci%C3%B3n%20de%20mis%20prendas.'" 
        target="_blank" 
        class="whatsapp-floating-circle"
        aria-label="Contacto Humano WhatsApp"
      >
        <svg class="ws-icon" viewBox="0 0 24 24">
          <path fill="#FFF" d="M12.031 6.172c-3.181 0-5.767 2.586-5.768 5.766-.001 1.298.424 2.517 1.209 3.507l-.795 2.903 2.973-.779A5.722 5.722 0 0 0 12.029 17.7c3.182 0 5.767-2.586 5.768-5.766-.001-3.182-2.587-5.762-5.766-5.762zm3.393 8.163c-.147.414-.744.757-1.029.802-.278.046-.57.068-.879-.033a5.07 5.07 0 0 1-2.423-1.417 5.61 5.61 0 0 1-1.31-2.1c-.139-.413-.016-.641.09-.763.102-.118.232-.278.349-.414.116-.135.155-.226.232-.376.078-.151.039-.283-.019-.396-.059-.114-.523-1.263-.717-1.728-.188-.453-.38-.391-.523-.399-.131-.006-.282-.008-.433-.008-.151 0-.396.057-.604.283-.207.227-.792.774-.792 1.888 0 1.113.811 2.188.924 2.34.113.151 1.562 2.385 3.791 3.344.53.227.944.364 1.267.466.533.17 1.018.146 1.4.089.426-.064 1.309-.536 1.493-1.053.184-.517.184-.961.129-1.053-.054-.093-.201-.147-.447-.269z"/>
        </svg>
      </a>
    </div>

  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import AdminModal from './components/AdminModal.vue';

export interface ServiceItem {
  id: string;
  name: string;
  price: number;
  category: string;
  tag: string;
  description?: string;
}

interface FaqItem {
  question: string;
  answer: string;
  isOpen: boolean;
}

// Control de Tema, Pasos y Modal Admin
const currentTheme = ref<'theme-silver' | 'theme-gold'>('theme-silver');
const currentStep = ref<'catalog' | 'checkout'>('catalog');
const isAdminOpen = ref(false);

// Categorías
const categories = [
  'Todos', 
  'Camisas & Blusas', 
  'Pantalones de Vestir', 
  'Faldas, Shorts & Cuero', 
  'Vestidos de Gala (Premium)', 
  'Poleras', 
  'Ropa Deportiva', 
  'Ropa Institutional', 
  'Ropa de Invierno', 
  'Suéteres & Polerones'
];

const selectedCategory = ref('Todos');

// BASE DE DATOS INICIAL PREDETERMINADA
const DEFAULT_SERVICES: ServiceItem[] = [
  // 1. Camisas y Blusas
  { id: 'cb-01', name: 'Reposición de botones (c/u)', price: 1500, category: 'Camisas & Blusas', tag: 'Compostura Básica' },
  { id: 'cb-02', name: 'Reparación de ojales (c/u)', price: 2000, category: 'Camisas & Blusas', tag: 'Compostura Básica' },
  { id: 'cb-03', name: 'Zurcido / Remiendo básico', price: 5000, category: 'Camisas & Blusas', tag: 'Reparación' },
  { id: 'cb-04', name: 'Hacer o profundizar pinzas de entalle', price: 5000, category: 'Camisas & Blusas', tag: 'Ajuste Calce' },
  { id: 'cb-05', name: 'Acortar largo total (Basta camisera)', price: 5500, category: 'Camisas & Blusas', tag: 'Modificación' },
  { id: 'cb-06', name: 'Dar vuelta al cuello (Voltear)', price: 6000, category: 'Camisas & Blusas', tag: 'Restauración' },
  { id: 'cb-07', name: 'Dar vuelta a los puños (Par)', price: 6000, category: 'Camisas & Blusas', tag: 'Restauración' },
  { id: 'cb-08', name: 'Transformación a manga corta', price: 6000, category: 'Camisas & Blusas', tag: 'Transformación' },
  { id: 'cb-09', name: 'Entallar costados', price: 7000, category: 'Camisas & Blusas', tag: 'Ajuste Silueta' },
  { id: 'cb-10', name: 'Estrechar mangas', price: 7000, category: 'Camisas & Blusas', tag: 'Ajuste Silueta' },
  { id: 'cb-11', name: 'Conversión a Cuello Mao', price: 8000, category: 'Camisas & Blusas', tag: 'Transformación' },
  { id: 'cb-12', name: 'Acortar mangas manteniendo puño original', price: 9000, category: 'Camisas & Blusas', tag: 'Modificación' },
  { id: 'cb-13', name: 'Cambio de cuello completo o de puños (Par)', price: 12000, category: 'Camisas & Blusas', tag: 'Restauración' },
  { id: 'cb-14', name: 'Ajuste de hombros estructural', price: 12000, category: 'Camisas & Blusas', tag: 'Alta Costura' },

  // 2. Pantalones de Vestir
  { id: 'pv-01', name: 'Basta a Máquina (Simple)', price: 4000, category: 'Pantalones de Vestir', tag: 'Basta Básica' },
  { id: 'pv-02', name: 'Crear o Profundizar Pinzas', price: 5000, category: 'Pantalones de Vestir', tag: 'Ajuste Calce' },
  { id: 'pv-03', name: 'Basta Invisible (Sastrera a mano)', price: 6000, category: 'Pantalones de Vestir', tag: 'Basta Premium' },
  { id: 'pv-04', name: 'Ajustar / Soltar Cintura (Por detrás)', price: 6000, category: 'Pantalones de Vestir', tag: 'Ajuste Sastrería' },
  { id: 'pv-05', name: 'Tubular Pantalón (Rodilla abajo)', price: 6000, category: 'Pantalones de Vestir', tag: 'Silueta' },
  { id: 'pv-06', name: 'Cambio de Cierre Tradicional', price: 6000, category: 'Pantalones de Vestir', tag: 'Reparación' },
  { id: 'pv-07', name: 'Refuerzo / Parche en entrepierna', price: 6000, category: 'Pantalones de Vestir', tag: 'Reparación' },
  { id: 'pv-08', name: 'Estrechar Piernas (Muslo a rodilla)', price: 8000, category: 'Pantalones de Vestir', tag: 'Silueta' },
  { id: 'pv-09', name: 'Basta con Protector de Talón / Cierre Invisible', price: 8000, category: 'Pantalones de Vestir', tag: 'Sastrería Especial' },
  { id: 'pv-10', name: 'Cambio de Forro de Bolsillo (Par)', price: 8000, category: 'Pantalones de Vestir', tag: 'Reparación' },
  { id: 'pv-11', name: 'Basta con Vuelta (Puño clásico)', price: 9000, category: 'Pantalones de Vestir', tag: 'Basta Clásica' },
  { id: 'pv-12', name: 'Arreglo de Tiro (Bolsas o tirantez)', price: 10000, category: 'Pantalones de Vestir', tag: 'Alta Costura' },
  { id: 'pv-13', name: 'Entalle Completo de Piernas', price: 12000, category: 'Pantalones de Vestir', tag: 'Ajuste Sastrería' },
  { id: 'pv-14', name: 'Ajuste de Cintura Completo (Rebaje costados)', price: 12000, category: 'Pantalones de Vestir', tag: 'Ajuste Sastrería' },

  // 3. Faldas, Shorts y Cuero
  { id: 'fc-01', name: 'Reparación de Abertura / Tajo trasero', price: 4000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $9.000)' },
  { id: 'fc-02', name: 'Basta de Short / Falda-Pantalón', price: 4500, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $10.000)' },
  { id: 'fc-03', name: 'Basta Falda Recta / Tubo', price: 5000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $12.000)' },
  { id: 'fc-04', name: 'Crear o Profundizar Pinzas', price: 5000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $10.000)' },
  { id: 'fc-05', name: 'Cambio de Cierre Short / Tradicional', price: 6000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $12.000)' },
  { id: 'fc-06', name: 'Ajustar Cintura / Pretina', price: 6500, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $14.000)' },
  { id: 'fc-07', name: 'Cambio de Cierre Invisible / Lateral', price: 6500, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $14.000)' },
  { id: 'fc-08', name: 'Entallar Costados (Cintura a Cadera)', price: 8000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $16.000)' },
  { id: 'fc-09', name: 'Basta Falda Larga / Vuelo / Plisada', price: 8000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $18.000)' },
  { id: 'fc-10', name: 'Mantener Basta Original con Detalle', price: 8000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $15.000)' },
  { id: 'fc-11', name: 'Rebajar Tiro en Short', price: 8500, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $16.000)' },
  { id: 'fc-12', name: 'Reparar o Cambiar Forro Interior', price: 10000, category: 'Faldas, Shorts & Cuero', tag: 'Telas (Cuero desde $18.000)' },

  // 4. Vestidos de Gala
  { id: 'vg-01', name: 'Incorporación de Copas / Push-up internas', price: 12000, category: 'Vestidos de Gala (Premium)', tag: 'Alta Costura' },
  { id: 'vg-02', name: 'Tomado de Cola (Sistema interno de baile)', price: 12000, category: 'Vestidos de Gala (Premium)', tag: 'Alta Costura' },
  { id: 'vg-03', name: 'Basta Invisible en Satén o Crepe', price: 15000, category: 'Vestidos de Gala (Premium)', tag: 'Basta Gala' },
  { id: 'vg-04', name: 'Ajuste de Hombros / Breteles con Pedrería', price: 15000, category: 'Vestidos de Gala (Premium)', tag: 'Alta Costura' },
  { id: 'vg-05', name: 'Cambio de Cierre Invisible de Gala', price: 15000, category: 'Vestidos de Gala (Premium)', tag: 'Reparación' },
  { id: 'vg-06', name: 'Basta de Gasa / Tul (Pañuelo o Rulete)', price: 18000, category: 'Vestidos de Gala (Premium)', tag: 'Basta Gala' },
  { id: 'vg-07', name: 'Fijación y Restauración de Pedrería (Por Hora)', price: 18000, category: 'Vestidos de Gala (Premium)', tag: 'Especialista' },
  { id: 'vg-08', name: 'Modificación de Escote (Profundizar/Cerrar)', price: 20000, category: 'Vestidos de Gala (Premium)', tag: 'Modificación' },
  { id: 'vg-09', name: 'Modelado / Entalle de Caderas (Efecto Sirena)', price: 20000, category: 'Vestidos de Gala (Premium)', tag: 'Silueta' },
  { id: 'vg-10', name: 'Basta de Múltiples Capas (Precio Inicial)', price: 25000, category: 'Vestidos de Gala (Premium)', tag: 'Basta Gala' },
  { id: 'vg-11', name: 'Ajustar Costados de Corsé estructurado', price: 25000, category: 'Vestidos de Gala (Premium)', tag: 'Alta Costura' },
  { id: 'vg-12', name: 'Soltar Vestido (Paneles en costados)', price: 25000, category: 'Vestidos de Gala (Premium)', tag: 'Modificación' },
  { id: 'vg-13', name: 'Conversión de Cierre a Corsé (Lace-up)', price: 35000, category: 'Vestidos de Gala (Premium)', tag: 'Transformación' },
  { id: 'vg-14', name: 'Basta con Aplicación / Subida de Encaje a mano', price: 35000, category: 'Vestidos de Gala (Premium)', tag: 'Alta Costura' },

  // 5. Poleras
  { id: 'po-01', name: 'Cerrar Desgarro o Agujero (Axilas/Hebilla)', price: 3500, category: 'Poleras', tag: 'Reparación Básica' },
  { id: 'po-02', name: 'Acortar Mangas de Polera', price: 4000, category: 'Poleras', tag: 'Modificación' },
  { id: 'po-03', name: 'Basta de Polera (Simple / Colleretera)', price: 4500, category: 'Poleras', tag: 'Basta' },
  { id: 'po-04', name: 'Estrechar Mangas de Polera', price: 4500, category: 'Poleras', tag: 'Ajuste Silueta' },
  { id: 'po-05', name: 'Cambiar o Ajustar Puño de Rib', price: 5000, category: 'Poleras', tag: 'Reparación' },
  { id: 'po-06', name: 'Reparar / Reducir Cuello Desbocado', price: 5000, category: 'Poleras', tag: 'Restauración' },
  { id: 'po-07', name: 'Entallar Costados (Regular a Slim)', price: 5000, category: 'Poleras', tag: 'Ajuste Silueta' },
  { id: 'po-08', name: 'Ajustar Cuello Polo (Piqué) u Off-shoulder', price: 6000, category: 'Poleras', tag: 'Modificación' },
  { id: 'po-09', name: 'Cambio de Tapeta / Cierre en Cuello Polo', price: 6000, category: 'Poleras', tag: 'Reparación' },
  { id: 'po-10', name: 'Ajustar Hombros Caídos (Rehacer Sisa)', price: 8000, category: 'Poleras', tag: 'Modificación' },
  { id: 'po-11', name: 'Transformación de Escote (Redondo a V)', price: 8500, category: 'Poleras', tag: 'Transformación' },

  // 6. Ropa Deportiva
  { id: 'rd-01', name: 'Poner Cordón de Ajuste en Pretina', price: 4000, category: 'Ropa Deportiva', tag: 'Compostura' },
  { id: 'rd-02', name: 'Basta de Short Microfibra / Running', price: 4500, category: 'Ropa Deportiva', tag: 'Basta' },
  { id: 'rd-03', name: 'Refuerzo de Costura en Entrepierna (Calzas)', price: 4500, category: 'Ropa Deportiva', tag: 'Ajuste Técnico' },
  { id: 'rd-04', name: 'Basta de Polera Deportiva (Dry-Fit)', price: 4500, category: 'Ropa Deportiva', tag: 'Basta' },
  { id: 'rd-05', name: 'Basta de Pantalón de Buzo (Simple)', price: 5000, category: 'Ropa Deportiva', tag: 'Basta' },
  { id: 'rd-06', name: 'Basta de Calza (Lycra elástica)', price: 5000, category: 'Ropa Deportiva', tag: 'Basta' },
  { id: 'rd-07', name: 'Entallar Polera Deportiva por costados', price: 5000, category: 'Ropa Deportiva', tag: 'Silueta' },
  { id: 'rd-08', name: 'Cambio de Elástico en Pretina / Breteles', price: 6000, category: 'Ropa Deportiva', tag: 'Reparación' },
  { id: 'rd-09', name: 'Ajustar Cintura de Calza (Pretina Alta)', price: 7000, category: 'Ropa Deportiva', tag: 'Ajuste Técnico' },
  { id: 'rd-10', name: 'Cambio de Cierre en Bolsillo o Chaqueta Ligera', price: 7000, category: 'Ropa Deportiva', tag: 'Reparación' },
  { id: 'rd-11', name: 'Estrechar Piernas de Buzo (Efecto Jogger)', price: 7500, category: 'Ropa Deportiva', tag: 'Silueta' },
  { id: 'rd-12', name: 'Basta de Buzo con Puño Elástico o Cierre', price: 9000, category: 'Ropa Deportiva', tag: 'Ajuste Técnico' },

  // 7. Ropa Institucional y Uniformes
  { id: 'ri-01', name: 'Instalación de Parches / Grados / Insignias (c/u)', price: 3000, category: 'Ropa Institutional', tag: 'Industrial' },
  { id: 'ri-02', name: 'Instalación de Velcro Hembra/Macho (Set)', price: 4500, category: 'Ropa Institutional', tag: 'Industrial' },
  { id: 'ri-03', name: 'Cambio o Confección de Presillas de Hombros', price: 5000, category: 'Ropa Institutional', tag: 'Modificación' },
  { id: 'ri-04', name: 'Reparación / Refuerzo de Bolsillos Tácticos', price: 5000, category: 'Ropa Institutional', tag: 'Reparación' },
  { id: 'ri-05', name: 'Basta Pantalón Táctico / Campaña (Ripstop)', price: 6000, category: 'Ropa Institutional', tag: 'Basta Reforzada' },
  { id: 'ri-06', name: 'Transformación de Camisa/Polera a Manga Corta', price: 6000, category: 'Ropa Institutional', tag: 'Transformación' },
  { id: 'ri-07', name: 'Refuerzo / Parche de Asiento Operativo', price: 7000, category: 'Ropa Institutional', tag: 'Reparación' },
  { id: 'ri-08', name: 'Entallar Camisa / Blusa Institucional (Pinzas)', price: 7500, category: 'Ropa Institutional', tag: 'Silueta' },
  { id: 'ri-09', name: 'Ajustar Cintura / Pretina Militar Reforzada', price: 8000, category: 'Ropa Institutional', tag: 'Ajuste Técnico' },
  { id: 'ri-10', name: 'Basta con Instalación de Elástico para Bota', price: 8500, category: 'Ropa Institutional', tag: 'Ajuste Técnico' },
  { id: 'ri-11', name: 'Estrechar Piernas (Entalle Táctico)', price: 9000, category: 'Ropa Institutional', tag: 'Silueta' },
  { id: 'ri-12', name: 'Acortar Mangas con Puño Táctico / Velcros', price: 10000, category: 'Ropa Institutional', tag: 'Modificación' },
  { id: 'ri-13', name: 'Cambio de Cierre Principal Overol / Guerrera', price: 12000, category: 'Ropa Institutional', tag: 'Reparación' },

  // 8. Ropa de Invierno
  { id: 'rv-01', name: 'Cambio de Puños de Lana / Rib en Casacas (Par)', price: 8000, category: 'Ropa de Invierno', tag: 'Telas Pesadas' },
  { id: 'rv-02', name: 'Basta de Casaca / Parka (Simple sin plumas)', price: 10000, category: 'Ropa de Invierno', tag: 'Telas Pesadas' },
  { id: 'rv-03', name: 'Acortar Mangas con Puño Elástico Interior', price: 12000, category: 'Ropa de Invierno', tag: 'Modificación' },
  { id: 'rv-04', name: 'Cambio de Cierre Principal en Parka / Casaca', price: 12000, category: 'Ropa de Invierno', tag: 'Reparación' },
  { id: 'rv-05', name: 'Entallar Casaca / Parka Urbana', price: 14000, category: 'Ropa de Invierno', tag: 'Silueta' },
  { id: 'rv-06', name: 'Cambio de Cierre en Abrigo Largo de Paño', price: 14000, category: 'Ropa de Invierno', tag: 'Reparación' },
  { id: 'rv-07', name: 'Acortar Mangas de Vestón o Abrigo Sastrero', price: 15000, category: 'Ropa de Invierno', tag: 'Modificación Sastrería' },
  { id: 'rv-08', name: 'Entallar Costados de Vestón / Abrigo (Con forro)', price: 16000, category: 'Ropa de Invierno', tag: 'Sastrería' },
  { id: 'rv-09', name: 'Basta de Abrigo Largo (Con Forro y abertura)', price: 18000, category: 'Ropa de Invierno', tag: 'Sastrería' },
  { id: 'rv-10', name: 'Ajuste de Hombros en Vestón/Abrigo (Bajar Talla)', price: 25000, category: 'Ropa de Invierno', tag: 'Sastrería Premium' },
  { id: 'rv-11', name: 'Cambio de Forro Interior Completo (Vestón)', price: 30000, category: 'Ropa de Invierno', tag: 'Reconstrucción' },
  { id: 'rv-12', name: 'Cambio de Forro Interior Completo (Abrigo Largo)', price: 40000, category: 'Ropa de Invierno', tag: 'Reconstrucción' },

  // 9. Chalecos, Suéteres, Polerones y Polares
  { id: 'cs-01', name: 'Instalación / Cambio de Cordón en Capucha', price: 3500, category: 'Suéteres & Polerones', tag: 'Compostura' },
  { id: 'cs-02', name: 'Basta de Polar / Micropolar Simple', price: 5000, category: 'Suéteres & Polerones', tag: 'Lana & Punto' },
  { id: 'cs-03', name: 'Zurcido de Punto (Agujeros en lana/hilo)', price: 5000, category: 'Suéteres & Polerones', tag: 'Restauración' },
  { id: 'cs-04', name: 'Acortar Mangas de Chaqueta Polar', price: 5500, category: 'Suéteres & Polerones', tag: 'Modificación' },
  { id: 'cs-05', name: 'Entallar Costados de Polar', price: 6000, category: 'Suéteres & Polerones', tag: 'Silueta' },
  { id: 'cs-06', name: 'Acortar Mangas de Polerón con Puño', price: 6000, category: 'Suéteres & Polerones', tag: 'Modificación' },
  { id: 'cs-07', name: 'Entallar Polerón (Silueta menos holgada)', price: 6500, category: 'Suéteres & Polerones', tag: 'Silueta' },
  { id: 'cs-08', name: 'Cambio de Puños o Banda de Cintura (Par)', price: 7000, category: 'Suéteres & Polerones', tag: 'Reparación' },
  { id: 'cs-09', name: 'Acortar Mangas de Chaleco Tejido (Fijación)', price: 7500, category: 'Suéteres & Polerones', tag: 'Lana & Punto' },
  { id: 'cs-10', name: 'Basta de Polerón (Desmontar y montar Rib)', price: 7500, category: 'Suéteres & Polerones', tag: 'Modificación' },
  { id: 'cs-11', name: 'Basta de Suéter / Chaleco (Sellado elástico)', price: 8000, category: 'Suéteres & Polerones', tag: 'Lana & Punto' },
  { id: 'cs-12', name: 'Entallar Costados de Suéter Tejido', price: 8000, category: 'Suéteres & Polerones', tag: 'Silueta' },
  { id: 'cs-13', name: 'Cambio de Cierre Principal (Polar o Polerón)', price: 8000, category: 'Suéteres & Polerones', tag: 'Reparación' }
];

const services = ref<ServiceItem[]>([]);

// Cargar y sincronizar con LocalStorage
const syncServicesFromStorage = () => {
  const stored = localStorage.getItem('app_prices');
  if (stored) {
    try {
      services.value = JSON.parse(stored);
    } catch {
      services.value = DEFAULT_SERVICES;
    }
  } else {
    services.value = DEFAULT_SERVICES;
    localStorage.setItem('app_prices', JSON.stringify(DEFAULT_SERVICES));
  }
};

onMounted(() => {
  syncServicesFromStorage();
});

const faqs = ref<FaqItem[]>([
  { question: '¿Cómo marco mi prenda si elijo Envío Directo?', answer: 'Es muy sencillo. Te pones la prenda en casa, mides el largo deseado frente a un espejo y fijas el doblez con un alfiler de gancho o de cabeza. Nosotros tomaremos exactamente esa medida en el taller.', isOpen: false },
  { question: '¿Qué pasa si no sé qué servicio técnico necesito?', answer: '¡No te preocupes! Haz clic en el botón redondo verde de WhatsApp abajo a la derecha. Un especialista te atenderá de inmediato de forma humana, podrás enviarle fotos de tu prenda y te guiará con el presupuesto.', isOpen: false },
  { question: '¿Cómo funciona el retiro y la entrega a domicilio?', answer: 'Una vez coordinado el pedido por WhatsApp, agendamos un bloque horario. Retiramos tus prendas en la puerta de tu hogar o conserjería, van al taller de costura, y te las devolvemos listas en la misma dirección.', isOpen: false },
  { question: '¿Mis prendas están seguras en el proceso?', answer: 'Absolutamente. Tratamos cada prenda con el máximo cuidado, registrando su ingreso. Además, nuestro compromiso ético y de calce perfecto garantiza que el trabajo se realiza con estándares de alta costura.', isOpen: false },
]);

const cart = ref<ServiceItem[]>([]);

const filteredServices = computed(() => {
  if (selectedCategory.value === 'Todos') return services.value;
  return services.value.filter(s => s.category === selectedCategory.value);
});

const cartTotal = computed(() => cart.value.reduce((sum, item) => sum + item.price, 0));
const isAdded = (id: string) => cart.value.some(item => item.id === id);

const toggleCart = (service: ServiceItem) => {
  const index = cart.value.findIndex(item => item.id === service.id);
  if (index > -1) {
    cart.value.splice(index, 1);
  } else {
    cart.value.push(service);
  }
};

const toggleFaq = (index: number) => {
  const item = faqs.value[index];
  if (!item) return;
  item.isOpen = !item.isOpen;
};

const deliveryMethod = ref<'domicilio' | 'tienda'>('domicilio');
const serviceType = ref<'estandar' | 'presencial'>('estandar');

const phoneNumber = '56973489845'; 

const sendToWhatsApp = (): void => {
  const itemsText = cart.value
    .map(item => `- ${item.name} (${item.tag}) -> $${item.price.toLocaleString('es-CL')}`)
    .join('%0A');
  const entrega = deliveryMethod.value === 'domicilio' ? '🛵 Retiro a domicilio' : '🏪 Dejaré en tienda';
  const tipo = serviceType.value === 'estandar' 
    ? '⚡ Envío directo (Marcado en casa)' 
    : '🪡 Requiere asesoría / medición presencial';
  let message = `¡Hola! Vengo de la plataforma y quiero coordinar un servicio:%0A%0A`;
  message += `*SERVICIOS SELECCIONADOS:*%0A${itemsText}%0A%0A`;
  message += `*LOGÍSTICA:* ${entrega}%0A`;
  message += `*TIPO DE ATENCIÓN:* ${tipo}%0A%0A`;
  message += `*TOTAL ESTIMADO BASE:* $${cartTotal.value.toLocaleString('es-CL')}%0A%0A`;
  message += `Me gustaría concretar el agendamiento y ver los horarios disponibles.`;
  window.open(`https://api.whatsapp.com/send?phone=${phoneNumber}&text=${message}`, '_blank');
};
</script>

<style scoped>
/* ==========================================================================
1. ESTILOS BASE Y DINÁMICOS POR TEMA
========================================================================== */
.app-container {
  min-height: 100vh;
  padding: 24px 16px 40px 16px;
  font-family: 'Inter', system-ui, -apple-system, sans-serif;
  transition: background-color 0.4s ease, color 0.4s ease;
  position: relative;
}

/* --- TEMA 1: BLANCO Y PLATA (theme-silver) --- */
.app-container.theme-silver {
  background-color: #f1f5f9;
  color: #1e293b;
}

.theme-silver .step-container {
  background: #ffffff;
  border: 1px solid #cbd5e1;
  box-shadow: 0 10px 30px rgba(148, 163, 184, 0.15);
}

.theme-silver .metallic-title {
  background: linear-gradient(135deg, #1e293b 0%, #475569 40%, #94a3b8 70%, #475569 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.theme-silver .brand-badge {
  background: #e2e8f0;
  color: #334155;
  border: 1px solid #94a3b8;
}

.theme-silver .subtitle, .theme-silver .procedure-card p, .theme-silver .service-desc {
  color: #64748b;
}

.theme-silver .section-title { color: #475569; }
.theme-silver .procedure-card h4, .theme-silver .service-card h4, .theme-silver .guarantee-section h3, .theme-silver .faq-question h4 {
  color: #0f172a;
}

.theme-silver .section-divider { border-top-color: #e2e8f0; }

.theme-silver .filter-bar button {
  background: #ffffff;
  color: #475569;
  border: 1px solid #cbd5e1;
}

.theme-silver .filter-bar button.active {
  background: linear-gradient(135deg, #64748b 0%, #334155 100%);
  color: #ffffff;
  border-color: #334155;
  box-shadow: 0 4px 12px rgba(51, 65, 85, 0.2);
}

.theme-silver .service-card {
  background: #ffffff;
  border: 1px solid #e2e8f0;
}

.theme-silver .service-tag { color: #475569; }
.theme-silver .price { color: #0f172a; }

.theme-silver .btn-add {
  border: 1px solid #94a3b8;
  color: #334155;
  background: #f8fafc;
}

.theme-silver .btn-add.added {
  background: #16a34a;
  color: #ffffff;
  border-color: #16a34a;
}

.theme-silver .guarantee-section, .theme-silver .faq-item {
  background: #f8fafc;
  border: 1px solid #cbd5e1;
}

.theme-silver .guarantee-badge {
  background: #e2e8f0;
  color: #334155;
}

.theme-silver .guarantee-section p, .theme-silver .faq-answer p { color: #475569; }

.theme-silver .floating-cart-bar {
  background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e1 50%, #94a3b8 100%);
  color: #0f172a;
  border: 1px solid #94a3b8;
}

.theme-silver .btn-continue {
  background: #0f172a;
  color: #ffffff;
}

.theme-silver .cart-summary {
  background: #f8fafc;
  border: 1px solid #e2e8f0;
}

.theme-silver .options-grid button {
  background: #ffffff;
  border: 1px solid #cbd5e1;
  color: #334155;
}

.theme-silver .options-grid button.active {
  background: #0f172a;
  color: #ffffff;
  border-color: #0f172a;
}

/* --- TEMA 2: NEGRO Y DORADO (theme-gold) --- */
.app-container.theme-gold {
  background-color: #0d0d0d;
  color: #f3f4f6;
}

.theme-gold .step-container {
  background: #141414;
  border: 1px solid #2a2415;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.8);
}

.theme-gold .metallic-title {
  background: linear-gradient(135deg, #bf953f 0%, #fcf6ba 25%, #b38728 50%, #fbf5b7 75%, #aa771c 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.theme-gold .brand-badge {
  background: #1e190e;
  color: #d4af37;
  border: 1px solid #54431e;
}

.theme-gold .subtitle, .theme-gold .procedure-card p, .theme-gold .service-desc { color: #a3a3a3; }
.theme-gold .section-title { color: #d4af37; }
.theme-gold .procedure-card h4, .theme-gold .service-card h4, .theme-gold .guarantee-section h3, .theme-gold .faq-question h4 {
  color: #f3f4f6;
}

.theme-gold .section-divider { border-top-color: #262626; }

.theme-gold .filter-bar button {
  background: #1a1a1a;
  color: #a3a3a3;
  border: 1px solid #333;
}

.theme-gold .filter-bar button.active {
  background: linear-gradient(135deg, #bf953f, #aa771c);
  color: #000;
  border-color: #d4af37;
  font-weight: 800;
}

.theme-gold .service-card {
  background: #1a1a1a;
  border: 1px solid #2a2415;
}

.theme-gold .service-tag { color: #d4af37; }
.theme-gold .price { color: #fcf6ba; }

.theme-gold .btn-add {
  border: 1px solid #bf953f;
  color: #d4af37;
  background: transparent;
}

.theme-gold .btn-add.added {
  background: linear-gradient(135deg, #bf953f, #aa771c);
  color: #000;
  border-color: #d4af37;
}

.theme-gold .guarantee-section, .theme-gold .faq-item {
  background: #19160e;
  border: 1px solid #3d3113;
}

.theme-gold .guarantee-badge {
  background: #2a220f;
  color: #d4af37;
}

.theme-gold .guarantee-section p, .theme-gold .faq-answer p { color: #d1d5db; }

.theme-gold .floating-cart-bar {
  background: linear-gradient(135deg, #bf953f, #aa771c);
  color: #000;
}

.theme-gold .btn-continue {
  background: #000;
  color: #d4af37;
}

.theme-gold .cart-summary {
  background: rgba(255, 255, 255, 0.05);
}

.theme-gold .options-grid button {
  background: #1a1a1a;
  border: 1px solid #333;
  color: #a3a3a3;
}

.theme-gold .options-grid button.active {
  background: linear-gradient(135deg, #bf953f, #aa771c);
  color: #000;
  border-color: #d4af37;
  font-weight: 700;
}

/* ==========================================================================
2. BANNER DE PRESENTACIÓN (ESTÁTICO EN ENCABEZADO)
========================================================================== */
.theme-switcher-bar {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  background: rgba(18, 18, 18, 0.85);
  backdrop-filter: blur(12px);
  padding: 8px 18px;
  border-radius: 30px;
  border: 1px solid rgba(255, 255, 255, 0.15);
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
  width: fit-content;
  margin: 0 auto 24px auto;
}

.theme-label {
  font-size: 11px;
  font-weight: 700;
  color: #e2e8f0;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.theme-toggle-group {
  display: flex;
  gap: 6px;
}

.btn-theme {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 700;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
  background: rgba(255, 255, 255, 0.1);
  color: #aaa;
}

.btn-theme.active {
  background: #ffffff;
  color: #000000;
  box-shadow: 0 2px 8px rgba(0,0,0,0.3);
}

.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.silver-dot { background: linear-gradient(135deg, #f8fafc, #64748b); }
.gold-dot { background: linear-gradient(135deg, #fcf6ba, #bf953f); }

/* ==========================================================================
3. ESTRUCTURA PRINCIPAL Y COMPONENTES
========================================================================== */
.step-container {
  max-width: 550px;
  margin: 0 auto 100px auto;
  border-radius: 24px;
  padding: 32px;
  transition: all 0.4s ease;
}

.catalog-header { text-align: center; margin-bottom: 28px; }
.brand-badge { display: inline-block; font-size: 10px; font-weight: 800; padding: 4px 10px; border-radius: 12px; margin-bottom: 8px; text-transform: uppercase; }
h2 { font-size: 24px; font-weight: 800; margin: 0 0 6px 0; }
.subtitle { font-size: 13px; margin: 0; }

.visual-procedure { margin-bottom: 24px; }
.section-title { font-size: 12px; font-weight: 800; text-transform: uppercase; text-align: center; margin-bottom: 14px; letter-spacing: 0.5px; }
.procedure-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
.procedure-card { text-align: center; }
.proc-image-wrapper { position: relative; width: 100%; aspect-ratio: 1; border-radius: 12px; overflow: hidden; margin-bottom: 6px; }
.proc-image-wrapper img { width: 100%; height: 100%; object-fit: cover; }
.step-number { position: absolute; top: 4px; left: 4px; background: #000; color: #fff; font-size: 9px; font-weight: 800; width: 16px; height: 16px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
.procedure-card h4 { font-size: 10px; font-weight: 700; margin: 0 0 2px 0; }
.procedure-card p { font-size: 8px; margin: 0; line-height: 1.1; }

.section-divider { border: 0; border-top: 1px solid #eee; margin: 28px 0; }

.filter-bar { display: flex; gap: 8px; overflow-x: auto; padding-bottom: 8px; margin-bottom: 20px; scrollbar-width: none; }
.filter-bar::-webkit-scrollbar { display: none; }
.filter-bar button { padding: 8px 14px; border-radius: 20px; font-size: 12px; font-weight: 600; white-space: nowrap; cursor: pointer; transition: all 0.2s; }

.services-grid { display: flex; flex-direction: column; gap: 10px; margin-bottom: 28px; }
.service-card { display: flex; justify-content: space-between; align-items: center; padding: 14px 16px; border-radius: 14px; }
.service-meta { flex: 1; padding-right: 12px; }
.service-tag { font-size: 8px; font-weight: 800; text-transform: uppercase; letter-spacing: 0.5px; }
.service-card h4 { margin: 2px 0; font-size: 14px; font-weight: 700; }
.service-desc { font-size: 11px; margin: 2px 0 4px 0; }
.price { font-size: 13px; font-weight: 800; margin: 2px 0 0 0; }
.empty-state { text-align: center; font-size: 13px; opacity: 0.6; padding: 20px 0; }

.btn-add { padding: 8px 14px; border-radius: 8px; font-size: 12px; font-weight: 700; cursor: pointer; transition: all 0.2s; }

.guarantee-section { border-radius: 14px; padding: 18px; margin-bottom: 28px; }
.guarantee-badge { display: inline-block; font-size: 10px; font-weight: 700; padding: 3px 8px; border-radius: 8px; margin-bottom: 6px; }
.guarantee-section h3 { font-size: 15px; font-weight: 700; margin: 0 0 6px 0; }
.guarantee-section p { font-size: 12px; line-height: 1.4; margin: 0; }

/* FAQ */
.faq-section { margin-bottom: 28px; }
.faq-container { display: flex; flex-direction: column; gap: 8px; }
.faq-item { border-radius: 12px; padding: 12px 16px; cursor: pointer; transition: all 0.2s ease; }
.faq-question { display: flex; justify-content: space-between; align-items: center; }
.faq-question h4 { font-size: 13px; font-weight: 700; margin: 0; }
.faq-arrow { font-size: 10px; opacity: 0.6; }
.faq-answer { margin-top: 8px; padding-top: 4px; border-top: 1px dashed rgba(150, 150, 150, 0.2); }
.faq-answer p { font-size: 12px; margin: 4px 0 0 0; line-height: 1.4; }

/* CARRITO FLOTANTE */
.floating-cart-bar { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: calc(100% - 32px); max-width: 518px; padding: 14px 20px; border-radius: 16px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 8px 25px rgba(0,0,0,0.3); z-index: 99; }
.cart-info { display: flex; flex-direction: column; }
.cart-info span { font-size: 10px; opacity: 0.8; }
.cart-info strong { font-size: 16px; }
.btn-continue { border: none; padding: 10px 16px; border-radius: 8px; font-weight: 800; font-size: 12px; cursor: pointer; }

/* CHECKOUT */
.btn-back { background: transparent; border: none; color: #888; font-size: 12px; font-weight: 700; cursor: pointer; margin-bottom: 16px; padding: 0; }
.cart-summary { padding: 16px; border-radius: 12px; margin: 16px 0; }
.cart-item { display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 13px; }
.item-title { display: block; font-weight: 600; }
.item-tag-sub { font-size: 10px; opacity: 0.7; }
.cart-total-row { display: flex; justify-content: space-between; margin-top: 12px; font-weight: 800; border-top: 1px dashed rgba(150,150,150,0.3); padding-top: 8px; }

.form-section { margin-bottom: 20px; }
.form-section h3 { font-size: 13px; font-weight: 700; margin-bottom: 10px; }
.options-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
.options-grid.vertical { grid-template-columns: 1fr; }
.options-grid button { padding: 12px; border-radius: 10px; font-size: 12px; font-weight: 600; cursor: pointer; text-align: left; transition: all 0.2s; }

.btn-whatsapp { width: 100%; background: #25D366; color: white; border: none; padding: 14px; border-radius: 12px; font-size: 14px; font-weight: 700; cursor: pointer; margin-top: 10px; box-shadow: 0 4px 12px rgba(37, 211, 102, 0.3); }

/* BOTÓN FLOTANTE ADMIN - FIJO EN LA ESQUINA INFERIOR IZQUIERDA */
.btn-admin-trigger {
  position: fixed;
  bottom: 20px;
  left: 20px;
  background: rgba(18, 18, 18, 0.85);
  color: #d4af37;
  border: 1px solid rgba(212, 175, 55, 0.4);
  padding: 10px 16px;
  border-radius: 30px;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
  z-index: 1000;
  backdrop-filter: blur(8px);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  transition: all 0.3s ease;
}

.btn-admin-trigger:hover {
  background: #d4af37;
  color: #000;
  border-color: #d4af37;
  transform: translateY(-2px);
}

/* WHATSAPP FLOTANTE */
.whatsapp-wrapper { position: fixed; bottom: 20px; right: 20px; z-index: 100; display: flex; align-items: center; gap: 8px; transition: bottom 0.3s ease; }
.whatsapp-wrapper.with-cart { bottom: 90px; }
.whatsapp-label { font-size: 10px; font-weight: 700; background: rgba(0,0,0,0.75); color: #fff; padding: 4px 8px; border-radius: 6px; backdrop-filter: blur(4px); display: none; }
@media (min-width: 480px) { .whatsapp-label { display: inline-block; } }
.whatsapp-floating-circle { width: 48px; height: 48px; background-color: #25D366; border-radius: 50%; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 12px rgba(37, 211, 102, 0.4); text-decoration: none; }
.ws-icon { width: 26px; height: 26px; }
</style>
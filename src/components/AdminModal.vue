<!-- src/components/AdminModal.vue -->
<template>
  <div v-if="isOpen" class="admin-backdrop" @click.self="handleClose">
    <div class="admin-card" role="dialog" aria-modal="true">
      <div class="admin-header">
        <h2>Panel de Administración</h2>
        <button class="close-btn" @click="handleClose" aria-label="Cerrar">&times;</button>
      </div>

      <!-- VISTA 1: LOGIN CON CLAVE -->
      <div v-if="!isAuthenticated" class="auth-section">
        <p class="auth-desc">Ingrese la clave maestra para gestionar precios y servicios:</p>
        <form @submit.prevent="login" class="auth-form">
          <input 
            ref="passwordInputRef"
            type="password" 
            v-model="passwordInput" 
            placeholder="Contraseña" 
            class="auth-input"
            required 
          />
          <p v-if="errorMessage" class="error-msg">{{ errorMessage }}</p>
          <button type="submit" class="btn-primary">Ingresar</button>
        </form>
      </div>

      <!-- VISTA 2: PANEL DE GESTIÓN DE PRECIOS -->
      <div v-else class="admin-content">
        <!-- Agregar nuevo servicio -->
        <form @submit.prevent="handleAdd" class="add-form">
          <h3>Agregar Nuevo Servicio</h3>
          <div class="form-group vertical">
            <input 
              v-model.trim="newTitle" 
              placeholder="Nombre del servicio" 
              required 
            />
            <div class="row-inputs">
              <select v-model="newCategory" class="category-select">
                <option value="Camisas & Blusas">Camisas & Blusas</option>
                <option value="Pantalones de Vestir">Pantalones de Vestir</option>
                <option value="Faldas, Shorts & Cuero">Faldas, Shorts & Cuero</option>
                <option value="Vestidos de Gala (Premium)">Vestidos de Gala (Premium)</option>
                <option value="Poleras">Poleras</option>
                <option value="Ropa Deportiva">Ropa Deportiva</option>
                <option value="Ropa Institutional">Ropa Institutional</option>
                <option value="Ropa de Invierno">Ropa de Invierno</option>
                <option value="Suéteres & Polerones">Suéteres & Polerones</option>
              </select>
              <input 
                v-model.number="newPrice" 
                type="number" 
                placeholder="Precio ($)" 
                required 
                min="0" 
              />
            </div>
            <button type="submit" class="btn-add">+ Agregar a Catálogo</button>
          </div>
        </form>

        <hr class="divider" />

        <!-- Lista editable de servicios -->
        <h3>Precios Actuales del Catálogo</h3>
        <div class="search-box">
          <input v-model="filterTerm" placeholder="Buscar servicio por nombre..." class="search-input" />
        </div>

        <ul class="price-list">
          <li v-for="item in filteredPrices" :key="item.id" class="price-item">
            <div class="item-info">
              <span class="item-title">{{ item.name }}</span>
              <span class="item-cat">{{ item.category }}</span>
            </div>
            <div class="item-actions">
              <span>$</span>
              <input 
                type="number" 
                v-model.number="item.price" 
                @change="savePrices"
                class="price-input"
                min="0"
              />
              <button 
                @click="removeService(item.id)" 
                class="btn-delete" 
                title="Eliminar"
                aria-label="Eliminar servicio"
              >&times;</button>
            </div>
          </li>
        </ul>

        <div class="admin-footer">
          <button class="btn-secondary" @click="logout">Cerrar Sesión</button>
          <button class="btn-primary" @click="handleClose">Guardar y Salir</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, computed, nextTick } from 'vue';
import type { ServiceItem } from '../App.vue';

const props = defineProps<{ isOpen: boolean }>();
const emit = defineEmits<{ 
  (e: 'close'): void;
  (e: 'update-services'): void;
}>();

// Clave de administrador
const ADMIN_PASSWORD = '19831984';

const isAuthenticated = ref(false);
const passwordInput = ref('');
const errorMessage = ref('');
const passwordInputRef = ref<HTMLInputElement | null>(null);

const prices = ref<ServiceItem[]>([]);
const filterTerm = ref('');

// Cargar lista existente
const loadPrices = (): ServiceItem[] => {
  const stored = localStorage.getItem('app_prices');
  if (!stored) return [];
  try {
    return JSON.parse(stored);
  } catch {
    return [];
  }
};

// Enfocar contraseña al abrir
watch(() => props.isOpen, (newVal) => {
  if (newVal) {
    prices.value = loadPrices();
    if (!isAuthenticated.value) {
      nextTick(() => passwordInputRef.value?.focus());
    }
  }
});

const savePrices = () => {
  localStorage.setItem('app_prices', JSON.stringify(prices.value));
  emit('update-services');
};

const newTitle = ref('');
const newCategory = ref('Camisas & Blusas');
const newPrice = ref<number | null>(null);

const handleAdd = () => {
  if (newTitle.value.trim() && newPrice.value !== null) {
    const newService: ServiceItem = {
      id: 'custom-' + Date.now().toString(),
      name: newTitle.value.trim(),
      price: newPrice.value,
      category: newCategory.value,
      tag: 'Personalizado'
    };
    prices.value.unshift(newService);
    savePrices();
    newTitle.value = '';
    newPrice.value = null;
  }
};

const removeService = (id: string) => {
  prices.value = prices.value.filter((p) => p.id !== id);
  savePrices();
};

const filteredPrices = computed(() => {
  if (!filterTerm.value.trim()) return prices.value;
  return prices.value.filter(p => p.name.toLowerCase().includes(filterTerm.value.toLowerCase()));
});

const login = () => {
  if (passwordInput.value.trim() === ADMIN_PASSWORD) {
    isAuthenticated.value = true;
    errorMessage.value = '';
    passwordInput.value = '';
  } else {
    errorMessage.value = 'Clave incorrecta.';
    passwordInput.value = '';
  }
};

const logout = () => {
  isAuthenticated.value = false;
};

const handleClose = () => {
  emit('close');
};
</script>

<style scoped>
.admin-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  backdrop-filter: blur(6px);
}

.admin-card {
  background: #141414;
  color: #fff;
  padding: 24px;
  border-radius: 16px;
  width: 90%;
  max-width: 520px;
  max-height: 85vh;
  overflow-y: auto;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.7);
  border: 1px solid #332815;
}

.admin-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.admin-header h2 {
  font-size: 1.2rem;
  margin: 0;
  color: #d4af37;
}

.close-btn {
  background: none;
  border: none;
  color: #aaa;
  font-size: 24px;
  cursor: pointer;
}

.auth-section, .auth-form {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.auth-desc {
  font-size: 0.9rem;
  color: #ccc;
  margin: 0;
}

.auth-input, .form-group input, .price-input, .category-select, .search-input {
  background: #232323;
  border: 1px solid #443722;
  color: #fff;
  padding: 10px 12px;
  border-radius: 6px;
  font-size: 0.9rem;
}

.auth-input:focus, .form-group input:focus, .price-input:focus {
  outline: 1px solid #d4af37;
}

.error-msg {
  color: #ff5555;
  font-size: 0.85rem;
  margin: 0;
}

.add-form {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.add-form h3 {
  font-size: 0.95rem;
  margin: 0 0 6px 0;
  color: #e2e8f0;
}

.form-group.vertical {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.row-inputs {
  display: flex;
  gap: 8px;
}

.category-select {
  flex: 1;
}

.divider {
  border: none;
  border-top: 1px solid #2a2a2a;
  margin: 18px 0;
}

.search-box {
  margin-bottom: 12px;
}

.search-input {
  width: 100%;
  box-sizing: border-box;
}

.price-list {
  list-style: none;
  padding: 0;
  margin: 12px 0 20px 0;
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-height: 280px;
  overflow-y: auto;
}

.price-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #1d1d1d;
  padding: 10px 12px;
  border-radius: 8px;
  border: 1px solid #282828;
}

.item-info {
  display: flex;
  flex-direction: column;
  gap: 2px;
  max-width: 60%;
}

.item-title {
  font-size: 0.85rem;
  font-weight: 600;
}

.item-cat {
  font-size: 0.7rem;
  color: #d4af37;
}

.item-actions {
  display: flex;
  align-items: center;
  gap: 6px;
}

.price-input {
  width: 80px;
  text-align: right;
  padding: 6px;
}

.btn-add {
  background: linear-gradient(135deg, #bf953f, #aa771c);
  color: #000;
  font-weight: bold;
  border: none;
  padding: 10px;
  border-radius: 6px;
  cursor: pointer;
}

.btn-delete {
  background: #e74c3c;
  color: white;
  border: none;
  border-radius: 4px;
  width: 28px;
  height: 28px;
  cursor: pointer;
  font-weight: bold;
}

.admin-footer {
  display: flex;
  gap: 10px;
  margin-top: 16px;
}

.btn-primary {
  background: linear-gradient(135deg, #bf953f, #aa771c);
  color: #000;
  font-weight: bold;
  border: none;
  padding: 10px 16px;
  border-radius: 6px;
  width: 100%;
  cursor: pointer;
}

.btn-secondary {
  background: #333;
  color: #fff;
  border: none;
  padding: 10px 16px;
  border-radius: 6px;
  cursor: pointer;
}
</style>
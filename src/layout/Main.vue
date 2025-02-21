<script setup>
import { onMounted, ref, computed, watch } from 'vue'
import axios from 'axios'
import Cards from '@/components/Cards.vue'

const items = ref([])
const materials = ref([])
const sortBy = ref('')
const selectedMaterial = ref('')
const favorites = ref(new Set(JSON.parse(localStorage.getItem('favorites')) || []))
const cart = ref(new Set(JSON.parse(localStorage.getItem('cart')) || []))

onMounted(async () => {
  try {
    const [itemsRes, materialsRes] = await Promise.all([
      axios.get('/data/items.json'),
      axios.get('/data/materials.json')
    ])
    items.value = itemsRes.data
    materials.value = materialsRes.data
  } catch (err) {
    console.log('Ошибка загрузки данных:', err)
  }
})

// Сохранение в localStorage при изменении favorites и cart
watch([favorites, cart], () => {
  localStorage.setItem('favorites', JSON.stringify([...favorites.value]))
  localStorage.setItem('cart', JSON.stringify([...cart.value]))
}, { deep: true })

const filteredItems = computed(() => {
  let result = [...items.value]

  if (selectedMaterial.value) {
    result = result.filter(item => String(item.material) === selectedMaterial.value)
  }

  if (sortBy.value === 'name') {
    result.sort((a, b) => a.name.localeCompare(b.name))
  } else if (sortBy.value === 'price.current_price') {
    result.sort((a, b) => a.price.current_price - b.price.current_price)
  } else if (sortBy.value === '-price.current_price') {
    result.sort((a, b) => b.price.current_price - a.price.current_price)
  }

  return result
})

// Функции для добавления и удаления из избранного и корзины
const toggleFavorite = (id) => {
  if (favorites.value.has(id)) {
    favorites.value.delete(id)
  } else {
    favorites.value.add(id)
  }
}

const toggleCart = (id) => {
  if (cart.value.has(id)) {
    cart.value.delete(id)
  } else {
    cart.value.add(id)
  }
}
</script>

<template>
  <section class="selects">
    <div class="container">
      <div class="selects__wrapper">
        <div class="selects__group">
          <label for="select-1" class="selects__label">Сортировать по:</label>
          <select v-model="sortBy" id="select-1" class="selects__input">
            <option value="">Без сортировки</option>
            <option value="name">Название</option>
            <option value="price.current_price">Цена (по возрастанию)</option>
            <option value="-price.current_price">Цена (по убыванию)</option>
          </select>
        </div>

        <div class="selects__group">
          <label for="select-2" class="selects__label">Материал:</label>
          <select v-model="selectedMaterial" id="select-2" class="selects__input">
            <option value="">Все материалы</option>
            <option v-for="material in materials" :key="material.id" :value="String(material.id)">
              {{ material.name }}
            </option>
          </select>
        </div>
      </div>
    </div>
  </section>

  <section class="items">
    <div class="container grid">
      <Cards
        :items="filteredItems"
        :favorites="favorites"
        :cart="cart"
        :onClickAdd="toggleCart"
        :onClickFavorite="toggleFavorite"
      />
    </div>
  </section>
</template>

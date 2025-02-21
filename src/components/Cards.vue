<script setup>
import { computed } from 'vue'

const props = defineProps({
  items: Array,
  favorites: Object,
  cart: Object,
  onClickAdd: Function,
  onClickFavorite: Function
})

const isFavorite = (id) => computed(() => props.favorites.has(id))
const isAdded = (id) => computed(() => props.cart.has(id))
</script>

<template>
  <div v-for="item in items" :key="item.id" class="items__card">
    <span v-if="item.price.old_price" class="items__card-discount">Скидка</span>
    <div class="items__card-image">
      <img :src="item.image.url" :alt="item.name" />
    </div>
    <div class="items__card-info">
      <p v-if="item.code" class="items__card-sku">{{ item.code }}</p>
      <h3 class="items__card-title">{{ item.name }}</h3>
      <div class="items__card-actions">
        <div class="items__card-price">
          <span v-if="item.price.old_price" class="items__card-old-price">
            {{ item.price.old_price.toFixed(0) }}₽
          </span>
          <span class="items__card-current-price">
            {{ item.price.current_price.toFixed(0) }}₽
          </span>
        </div>
        <div class="items__card-controls">
          <button @click="onClickFavorite(item.id)" class="btn items__favorite" aria-label="Добавить в избранное">
            <img :src="!isFavorite(item.id).value ? '/icons/favorite.svg' : '/icons/favorite-filled.svg'" alt="">
          </button>
          <button @click="() => onClickAdd(item.id)" class="btn items__cart" aria-label="Добавить в корзину">
            <img :src="!isAdded(item.id).value ? '/icons/cart.svg' : '/icons/confirm.svg'" alt="">
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

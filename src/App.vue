<script setup>
import { provide, ref, watch, computed } from 'vue';
import axios from 'axios';

import Header from './components/Header.vue';
import Drawer from './components/Drawer.vue';

const cart = ref([]);

const drawerOpen = ref(false);

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100));



const closeDrawer = () => {
  drawerOpen.value = false;
};

const openDrawer = () => {
  drawerOpen.value = true;
};

const addToCart = (item) => {
  cart.value.push(item);
  item.isAdded = true;
};

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1);
  item.isAdded = false;
};

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
};

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const params = {
        item_id: item.id,
        isFavorite: true
      };
      item.isFavorite = true;

      const { data } = await axios.post('https://19ff4f4eb11cd3d5.mokky.dev/favorites', params);

      item.favoriteId = data.id;

      console.log(data);
    } else {
      
      await axios.delete(`https://19ff4f4eb11cd3d5.mokky.dev/favorites/${item.favoriteId}`);
      item.isFavorite = false;
      item.favoriteId = null;
    }
  } catch (error) {
    console.log(error);
  }
};

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value));
  },
  {
    deep: true
  }
);

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
});

provide('favorite', {
  addToFavorite,
  onClickAddPlus
});

</script>

<template>
  <Drawer
    v-if="drawerOpen"
    :total-price="totalPrice"
    :vat-price="vatPrice"
  />

  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>

<style scoped></style>

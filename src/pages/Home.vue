<script setup>
import axios from 'axios';
import debounce from 'lodash.debounce';
import { inject, onMounted, reactive, ref, watch } from 'vue';
import CardList from '../components/CardList.vue';

const { cart } = inject('cart');

const items = ref([]);
const { addToFavorite, onClickAddPlus } = inject('favorite');

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
});



const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value;
}, 500);

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://19ff4f4eb11cd3d5.mokky.dev/favorites');
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.item_id === item.id);

      if (!favorite) {
        return item;
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      };
    });
  } catch (error) {
    console.log(error);
  }
};

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    };

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`;
    }

    const { data } = await axios.get('https://19ff4f4eb11cd3d5.mokky.dev/items', {
      params
    });
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }));
  } catch (error) {
    console.log(error);
  }
};

watch(filters, fetchItems);

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }));
});

onMounted(async () => {
  const localCart = localStorage.getItem('cart');
  cart.value = localCart ? JSON.parse(localCart) : [];

  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }));
});

</script>

<template>
  <div class="flex justify-between items-center mb-8">
    <h2 class="text-3xl font-bold">All Sneakers</h2>

    <div class="flex gap-4">
      <select
        @change="onChangeSelect"
        class="py-2 px-3 border rounded-md outline-none text-slate-400"
      >
        <option value="name">By name</option>
        <option value="price">By price (cheap)</option>
        <option value="-price">By price (expensive)</option>
      </select>
      <div class="relative">
        <img class="absolute left-3 top-3" src="/search.svg" alt="Search" />
        <input
          @input="onChangeSearchInput"
          class="border rounded-md py-2 pl-9 pr-4 outline-none focus:border-gray-400"
          type="text"
          placeholder="Search..."
        />
      </div>
    </div>
  </div>

  <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
</template>

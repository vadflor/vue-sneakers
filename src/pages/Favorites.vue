<script setup>
import axios from 'axios';
import { inject, onMounted, ref } from 'vue';

import CardList from '../components/CardList.vue';

const favorites = ref([]);

const { addToFavorite, onClickAddPlus } = inject('favorite');

function transformArray(array) {
  return array.map(item => {
        return {
            id: item.item.id,
            favoriteId: item.id,
            title: item.item.title,
            price: item.item.price,
            imageUrl: item.item.imageUrl,
            isFavorite: item.isFavorite
        };
    });
}

const fetchFavorites = async () => {
  try {
    const { data } = await axios.get(
      'https://19ff4f4eb11cd3d5.mokky.dev/favorites?_relations=items'
    );
    favorites.value = transformArray(data);
    console.log("value: ", favorites.value);
  } catch (error) {
    console.log(error);
  }
};

const addRemoveFavorites = async (item) => {

  await addToFavorite(item);
  await fetchFavorites();
  console.log(item);
}


onMounted(async () => {
  await fetchFavorites();
});

</script>

<template>
  <h2 class="text-3xl font-bold">My Favorites</h2>

  <CardList :items="favorites" @add-to-favorite="addRemoveFavorites" @add-to-cart="onClickAddPlus" />
</template>

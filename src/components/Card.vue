<script setup>

  defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickFavorite: Function,
    onClickAdd: Function
  });

</script>

<template>
  <div
    class="flex flex-col relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
  >
    <img 
      :src="!isFavorite ? '/like-1.svg':'like-2.svg'" 
      alt="Add to favorites" 
      class="absolute top-8 left-8" 
      @click="onClickFavorite"
    />
    <img :src="imageUrl" alt="Sneaker" class="h-[167px] w-[198px] object-contain"/>
    <p class="mt-2 flex-1">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Price:</span>
        <b>${{ price }}</b>
      </div>
      <img @click="onClickAdd" :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Add" />
    </div>
  </div>
</template>

<script setup>
  const emit = defineEmits(['onClickRemove']);
  
  defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number
  });
</script>

<template>
  <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
    <img 
      :src="imageUrl" 
      :alt="title" 
      class="w-16 h-16"
    />

    <div class="flex flex-col w-full">
      <p>{{ title }}</p>

      <div class="flex justify-between mt-2">
        <b>${{ price }}</b>
        <img
          @click="emit('onClickRemove')"
          class="opacity-40 hover:opacity-100 transition cursor-pointer" 
          src="/close.svg" 
          alt="close"
        >
      </div>
    </div>
  </div>
</template>
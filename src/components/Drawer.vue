<script setup>
import axios from 'axios';
import { computed, inject, ref } from 'vue';
import CartItemList from './CartItemList.vue';
import InfoBlock from './InfoBlock.vue';

const { closeDrawer } = inject('cart');

const { cart } = inject('cart');

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number
});

const isCreatingOrder = ref(false);
const orderId = ref(null);

const createOrder = async () => {
  isCreatingOrder.value = true;
  try {
    const { data } = await axios.post('https://19ff4f4eb11cd3d5.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice.value
    });
    cart.value = [];

    orderId.value = data.id;
  } catch (error) {
    console.log(error);
  } finally {
    isCreatingOrder.value = false;
  }
};

const cartIsEmpty = computed(() => cart.value.length === 0);
const cartButtonDisabled = computed(() => isCreatingOrder.value || cartIsEmpty.value);
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <div class="flex flex-col h-full">
      <div class="flex items-center gap-5 mb-8">
        <svg
          @click="closeDrawer"
          class="opacity-30 rotate-180 cursor-pointer hover:opacity-100 transition hover:scale-110"
          width="7"
          height="12"
          viewBox="0 0 7 12"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="M1 0.999999L6 6L1 11"
            stroke="black"
            stroke-width="1.5"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
        </svg>
        <h2 class="text-2xl font-bold">Cart</h2>
      </div>
      <div class="flex-1">
         

        <div v-if="!totalPrice || orderId" class="h-full flex items-center justify-center -mt-8">
          <InfoBlock
            v-if="!totalPrice && !orderId"
            title="Your cart is empty"
            description="Go shopping"
            image-url="/package-icon.png"
          />

          <InfoBlock
            v-if="orderId"
            title="Your order is created!"
            :description="`Your order #${orderId} will soon be transferred to courier delivery`"
            image-url="/order-success-icon.png"
          />
        </div>

        <CartItemList v-if="totalPrice" />
      </div>

      <div v-if="totalPrice" class="space-y-4 mt-7">
        <div class="flex gap-1 items-baseline">
          <span>Total:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>${{ totalPrice }}</b>
        </div>
        <div class="flex gap-1 items-baseline">
          <span>Taxes 5%:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>${{ vatPrice }}</b>
        </div>

        <button
          type="button"
          :disabled="cartButtonDisabled"
          @click="createOrder"
          class="bg-lime-500 active:bg-lime-600 mt-4 w-full rounded-xl py-3 text-white disabled:bg-slate-300"
        >
          Order now
        </button>
      </div>
    </div>
  </div>
</template>

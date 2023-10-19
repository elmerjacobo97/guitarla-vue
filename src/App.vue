<script setup>
import { reactive, ref, onMounted, watch } from "vue";
import { guitars as guitarsData } from "./data/guitars";
import Guitar from "./components/Guitar.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

const guitars = ref([]);
const cart = ref([]);
const guitar = ref({});

// Watch, escuchar cambios en el carrito
watch(
  cart,
  () => {
    saveCart();
  },
  { deep: true }
);

// Cuando el componente se monta
onMounted(() => {
  guitars.value = guitarsData;
  guitar.value = guitarsData[3];

  // Leer el carrito de localStorage
  const storageCart = JSON.parse(localStorage.getItem("cart"));
  if (storageCart) {
    cart.value = storageCart;
  }
});

// Method handlers
const addToCart = (guitar) => {
  // Comprobar si ya está en el carrito
  const existsInCart = cart.value.find((item) => item.id === guitar.id);
  if (existsInCart) {
    existsInCart.quantity++;
    return;
  }

  guitar.quantity = 1;
  cart.value.push(guitar);
};

// Guardar el carrito en localStorage
const saveCart = () => {
  localStorage.setItem("cart", JSON.stringify(cart.value));
};

const decrementQuantity = (id) => {
  const index = cart.value.findIndex((item) => item.id === id);
  if (cart.value[index].quantity <= 1) return;
  cart.value[index].quantity--;
};

const incrementQuantity = (id) => {
  const index = cart.value.findIndex((item) => item.id === id);
  if (cart.value[index].quantity >= 10) return;
  cart.value[index].quantity++;
};

const deleteFromCart = (id) => {
  cart.value = cart.value.filter((item) => item.id !== id);
};

const emptyCart = () => {
  cart.value = [];
};
</script>

<template>
  <Header :cart="cart" :guitar="guitar" @decrement-quantity="decrementQuantity" @increment-quantity="incrementQuantity"
    @add-to-cart="addToCart" @delete-from-cart="deleteFromCart" @empty-cart="emptyCart" />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>
    <div class="row mt-5">
      <!-- : es un alias para v-bind; @ es un alias para v-on -->
      <Guitar v-for="guitar in guitars" :key="guitar.id" :guitar="guitar" @add-to-cart="addToCart" />
    </div>
  </main>
  <Footer />
</template>

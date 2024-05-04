<script setup>
import { ref, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(
  carrito,
  () => {
    guardarLocalStorage();
  },
  { deep: true }
);

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];
  cargarLocalStorage();
});

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const cargarLocalStorage = () => {
  if (localStorage.getItem("carrito")) {
    carrito.value = JSON.parse(localStorage.getItem("carrito"));
  }
};

const agregarCarrito = (guitarra) => {
  const existeEnElCarrito = carrito.value.findIndex(
    (item) => item.id === guitarra.id
  );
  if (existeEnElCarrito !== -1) {
    carrito.value[existeEnElCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

const incrementarCantidad = (id) => {
  const producto = carrito.value.find((item) => item.id === id);
  producto.cantidad++;
};

const decrementarCantidad = (id) => {
  const producto = carrito.value.find((item) => item.id === id);
  if (producto.cantidad > 1) {
    producto.cantidad--;
  }
};

const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter((item) => item.id !== id);
};

const vaciarCarrito = () => {
  carrito.value = [];
  localStorage.removeItem("carrito");
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @eliminar-producto="eliminarProducto"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @vaciar-carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
      />
    </div>
  </main>
  <Footer />
</template>

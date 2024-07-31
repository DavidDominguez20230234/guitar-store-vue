<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarras from "./components/Guitarras.vue";
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
  {
    deep: true,
  }
);

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];

  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );
  if (existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }

  guardarLocalStorage();
};

const decrementarCantidad = (id) => {
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};

const incrementarCantidad = (id) => {
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad >= 5) return;
  carrito.value[index].cantidad++;
};

const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter((producto) => producto.id !== id);
};

const vaciarCarrito = () => {
  carrito.value = [];
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad="decrementarCantidad"
    @incrementar-cantidad="incrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-produto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarras
        v-for="guitarra in guitarras"
        v-bind:guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
      <!-- FIN GUITARRA -->
    </div>
  </main>
  <Footer />
</template>

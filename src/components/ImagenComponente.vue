<template>
  <div class="espacioImagen">
    <img :src="imageSrc" alt="Imagen de inicio para el Juego" />
    <p>{{ name }}</p>
  </div>
</template>

<script>
export default {
  props: {
    imageSrc: {
      type: String,
      required: true,
    },
    name: {
      type: String,
      required: true,
    },
  },
};
</script>

<style >
.espacioImagen {
  text-align: center;
}
.espacioImagen img {
  width: 250px;
  height: 250px;
}
</style>

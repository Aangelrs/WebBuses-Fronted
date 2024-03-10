<template>
  <v-app>
    <div>
      <v-img
        class="mx-auto my-0"
        max-width="200"
        src="../public/logo.jpeg"
      ></v-img>
  
      <v-card
        class="mx-auto pa-12 pb-8"
        elevation="8"
        max-width="448"
        rounded="lg"
      >
        <div class="text-subtitle-1 text-medium-emphasis">Usuario</div>
  
        <v-text-field
          density="compact"
          placeholder="nombre de Usuario"
          prepend-inner-icon="mdi-email-outline"
          variant="outlined"
        ></v-text-field>
  
        <div class="text-subtitle-1 text-medium-emphasis d-flex align-center justify-space-between">
            Contraseña
  
          <a
            class="text-caption text-decoration-none text-blue"
            href="#"
            rel="noopener noreferrer"
            target="_blank"
          >
            Se te olvido la contraseña?</a>
        </div>
  
        <v-text-field
          :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
          :type="visible ? 'text' : 'password'"
          density="compact"
          placeholder="Ingresa su contrasena"
          prepend-inner-icon="mdi-lock-outline"
          variant="outlined"
          @click:append-inner="visible = !visible"
        ></v-text-field>
  
        <v-card
          class="mb-12"
          color="surface-variant"
          variant="tonal"
        >
          <v-card-text class="text-medium-emphasis text-caption">
            Advertencia: Despues de tres intentos fallidos consecutivos de inicion de sesion. Su cuenta se bloqueara. Por favor contacte con un administrador de sistemas.
          </v-card-text>
        </v-card>
  
        <v-btn
          class="mb-8"
          color="blue"
          size="large"
          variant="tonal"
          block
          to="/"
        >
          Entrar
        </v-btn>
      </v-card>
    </div>
  </v-app>
  </template>

<script>
export default {
  data: () => ({
    visible: false,
  }),
}
</script>

<script setup>
definePageMeta({
  layout: false,  //Para desactivar el layout en esta pagina
});
</script>
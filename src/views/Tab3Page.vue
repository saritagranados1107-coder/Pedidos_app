<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Crear Post</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Crear Post</ion-title>
        </ion-toolbar>
      </ion-header>

      <form @submit.prevent="crearPost">
        <ion-item>
          <ion-input
            v-model="form.title"
            label="Título"
            label-placement="floating"
            placeholder="Escribe el título"
            required
          />
        </ion-item>

        <ion-item>
          <ion-textarea
            v-model="form.body"
            label="Contenido"
            label-placement="floating"
            placeholder="Escribe el contenido"
            :rows="5"
            required
          />
        </ion-item>

        <ion-item>
          <ion-input
            v-model.number="form.userId"
            type="number"
            label="User ID"
            label-placement="floating"
            min="1"
            required
          />
        </ion-item>

        <ion-button
          type="submit"
          expand="block"
          class="ion-margin-top"
          :disabled="loading || !formValido"
        >
          <ion-spinner v-if="loading" name="crescent" />
          <span v-else>Crear post</span>
        </ion-button>
      </form>

      <ion-card v-if="postCreado">
        <ion-card-header>
          <ion-card-subtitle>Post creado (ID: {{ postCreado.id }})</ion-card-subtitle>
          <ion-card-title>{{ postCreado.title }}</ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <p>{{ postCreado.body }}</p>
          <small>Usuario: {{ postCreado.userId }}</small>
        </ion-card-content>
      </ion-card>

      <ion-toast
        :is-open="toast.open"
        :message="toast.message"
        :color="toast.color"
        :duration="2500"
        @didDismiss="toast.open = false"
      />
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { reactive, ref, computed } from 'vue';
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonItem, IonInput, IonTextarea,
  IonButton, IonSpinner, IonCard, IonCardHeader, IonCardSubtitle, IonCardTitle, IonCardContent, IonToast,
} from '@ionic/vue';

interface Post {
  id?: number;
  title: string;
  body: string;
  userId: number;
}

const form = reactive<Post>({
  title: '',
  body: '',
  userId: 1,
});

const loading = ref(false);
const postCreado = ref<Post | null>(null);
const toast = reactive({ open: false, message: '', color: 'success' });

const formValido = computed(
  () => form.title.trim() !== '' && form.body.trim() !== '' && form.userId > 0
);

const mostrarToast = (message: string, color: 'success' | 'danger') => {
  toast.message = message;
  toast.color = color;
  toast.open = true;
};

const crearPost = async () => {
  if (!formValido.value) return;

  loading.value = true;
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
      method: 'POST',
      body: JSON.stringify({
        title: form.title,
        body: form.body,
        userId: form.userId,
      }),
      headers: {
        'Content-type': 'application/json; charset=UTF-8',
      },
    });

    if (!response.ok) throw new Error(`Error ${response.status}`);

    postCreado.value = await response.json();
    mostrarToast('Post creado correctamente', 'success');

    // Limpiar formulario
    form.title = '';
    form.body = '';
  } catch (error) {
    console.error(error);
    mostrarToast('No se pudo crear el post', 'danger');
  } finally {
    loading.value = false;
  }
};
</script>

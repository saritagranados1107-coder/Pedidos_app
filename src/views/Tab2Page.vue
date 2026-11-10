<!--EN ESTA PARTE SE ESTÁ USANDO DummyJSON PARA EL DESARROLLO, YA QUE ESTA API YA INCORPORA AUTENTICACIÓN POR TOKEN -->

<!--credenciales para iniciar sesión
usuario: emilys
contraseña: emilyspass
-->

<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 2 - API con Token</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">

      <!-- LOGIN -->
      <div v-if="!token">
        <h2>Iniciar sesión</h2>

        <ion-item>
          <ion-input
            v-model="username"
            label="Usuario"
            placeholder="Ingrese su usuario"
          />
        </ion-item>

        <ion-item>
          <ion-input
            v-model="password"
            label="Contraseña"
            type="password"
            placeholder="Ingrese su contraseña"
          />
        </ion-item>

        <ion-button
          expand="block"
          @click="iniciarSesion"
          :disabled="cargando"
        >
          {{ cargando ? 'Ingresando...' : 'Iniciar sesión' }}
        </ion-button>

        <p v-if="error" style="color: red">
          {{ error }}
        </p>
      </div>

      <!-- API PROTEGIDA -->
      <div v-else>
        <h2>Datos protegidos</h2>

        <ion-button @click="cargarDatos">
          Consultar API
        </ion-button>

        <ion-button color="danger" @click="cerrarSesion">
          Cerrar sesión
        </ion-button>

        <p v-if="error" style="color: red">
          {{ error }}
        </p>

        <ion-list>
          <ion-item v-for="user in users" :key="user.id">
            <ion-label>
              <h2>{{ user.firstName }} {{ user.lastName }}</h2>
              <p>{{ user.email }}</p>
            </ion-label>
          </ion-item>
        </ion-list>
      </div>

    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage, IonHeader, IonToolbar, IonTitle,
  IonContent, IonItem, IonInput, IonButton,
  IonList, IonLabel
} from '@ionic/vue';

import { ref } from 'vue';

const username = ref('emilys');
const password = ref('emilyspass');

const token = ref('');
const users = ref<any[]>([]);
const error = ref('');
const cargando = ref(false);

// LOGIN Y GENERACIÓN DEL TOKEN
async function iniciarSesion() {
  error.value = '';
  cargando.value = true;

  try {
    const response = await fetch(
      'https://dummyjson.com/auth/login',
      {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          username: username.value,
          password: password.value
        })
      }
    );

    const data = await response.json();

    if (!response.ok) {
      throw new Error(data.message || 'Credenciales incorrectas');
    }

    token.value = data.accessToken;

    await cargarDatos();

  } catch (e: any) {
    error.value = e.message;
    token.value = '';
  } finally {
    cargando.value = false;
  }
}

// CONSULTA PROTEGIDA CON BEARER TOKEN
async function cargarDatos() {
  error.value = '';
  users.value = [];

  try {
    if (!token.value) {
      throw new Error('401: No hay token de autenticación');
    }

    const response = await fetch(
      'https://dummyjson.com/auth/users',
      {
        headers: {
          'Authorization': `Bearer ${token.value}`
        }
      }
    );

    const data = await response.json();

    if (!response.ok) {
      throw new Error(
        `${response.status}: ${data.message || 'Error de autenticación'}`
      );
    }

    users.value = data.users;

  } catch (e: any) {
    error.value = e.message;
    token.value = '';
  }
}

// CERRAR SESIÓN
function cerrarSesion() {
  token.value = '';
  users.value = [];
  error.value = '';
}
</script>
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 2</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 2</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-list>
        <ion-item v-for="post in posts" :key="post.id">
          <ion-label>
            <h2>{{ post.title }}</h2>
            <p>{{ post.body }}</p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonList, IonItem, IonLabel } from '@ionic/vue';
import { ref } from 'vue';

type Posts = {
  id: number;
  title: string;
  body: string;
};

const posts = ref<Posts[]>([]);

function cargarPosts(){

  fetch('https://jsonplaceholder.typicode.com/posts')
  .then(response => response.json())
  .then(json => {
    console.log(json);
    posts.value = json;

  })

}
cargarPosts();
</script>

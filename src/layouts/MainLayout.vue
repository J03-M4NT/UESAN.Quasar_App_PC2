<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="bg-primary text-white">
      <q-toolbar class="column items-center q-pt-md q-pb-md">
        <img src="~assets/convertion-logo.webp" alt="Logo Conversion" style="height: 144px; width: auto; margin-bottom: 8px;" />
        <q-toolbar-title class="text-center q-pa-none" style="width: 100%;">
          <span style="font-weight: bold;">Conversión App</span>
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
</script>

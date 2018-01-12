<template>
  <div id="app">
    <v-app>
      <app-header></app-header>
      <section class="app-main">
        <router-view />
      </section>
      <app-footer></app-footer>
    </v-app>
  </div>
</template>

<script>
import AppHeader from './components/Header';
import AppFooter from './components/Footer';
export default {
  name: 'app',
  components: {
    AppHeader,
    AppFooter
  }
}
</script>

<style lang="stylus">
@import './assets/stylus/vuetify.styl';
</style>

<style lang="scss">
@import 'assets/scss/main.scss';
</style>

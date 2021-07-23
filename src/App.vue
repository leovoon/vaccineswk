<template>
  <div id="app">
    <h2>Daily Vaccination 💉 in Malaysia</h2>
    <div class="nav">
      <router-link to="/" exact>Home</router-link>
      <router-link to="/about">About</router-link>
    </div>

    <keep-alive>
      <router-view></router-view>
    </keep-alive>

    <footer><p style="font-size: small">Made with ❤️ by leovoon</p></footer>
  </div>
</template>

<script>
let { Home } = await import('./components/Home.vue')
let { About } = await import('./components/About.vue')

export default {
  name: 'App',
  components: { Home, About },
}
</script>

<style>
#app {
  display: grid;
  align-items: center;
  justify-items: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 15px;
  overflow-x: hidden;
}

.nav {
  margin-bottom: 30px;
}

.nav > * {
  margin: 0.5rem 0.5rem;
  text-decoration: none;
  color: gray;
  padding: 3px 6px;
  transition: ease-in-out 200ms;
}

.nav .router-link-active,
.nav .router-link-exact-active {
  color: white;
  background-color: lightskyblue;
  cursor: pointer;
  border-radius: 5px;
}

.fade-enter-active,
.fade-leave-active {
  transition-property: height, opacity;
  transition-timing-function: ease;
  transition-duration: 0.3s;
  overflow: hidden;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
@media screen and (max-width: 420px) {
  h2 {
    width: 240px;
  }
}
</style>

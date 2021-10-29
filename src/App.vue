<template>
  <div id="app">
    <div id="nav">
      <v-row>
        <img src="@/assets/drawing-with-label.svg" alt="logo" width="200" />
        <router-link to="/">Home</router-link> |
        <router-link to="/about">About</router-link>
      </v-row>
    </div>
    <router-view />
  </div>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>

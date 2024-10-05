<template>
  <router-view/>
</template>

<style>
html, body {
  margin: 0;
  padding: 0;
  height: 100%;
  background-color: #121212; /* Dark background color */
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #121212; /* Dark background color */
  min-height: 100vh; /* Ensure it covers the full height of the screen */
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #ffffff; /* Light text color for contrast */
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>

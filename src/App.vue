<template>
  <div id="app" v-bind:class="[this.$store.state.popupState]">
    <router-view/>
  </div>
</template>

<style>
* {
  border: 0;
  padding: 0;
  margin: 0;
  outline: none;
  box-sizing: border-box;
  font-family: 'Montserrat', sans-serif;
}

.container {
  width: 100%;
  max-width: 840px;
  margin: 0 auto;
}

@media (max-width: 980px){
  .container {
    width: 100%;
    max-width: 540px;
    margin: 0 auto;
  }
}

@media (max-width: 980px){
  .container {
    width: 100%;
    max-width: 540px;
    margin: 0 auto;
  }
}

@media (max-width: 560px){
  .container {
    width: 100%;
    max-width: 340px;
    margin: 0 auto;
  }
}
</style>

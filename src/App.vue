<template>
  <div class="challenge">
    <img id="company-logo" alt="Vue logo" src="./assets/logo.png">
    <Challenge msg="Welcome to the challenge 🐰"/>
  </div>
</template>

<script lang="ts">
import Challenge from './components/Challenge.vue'
import { Component, Vue } from "vue-property-decorator";

@Component({ components: { Challenge } })
export default class App extends Vue {
  mounted(): void {
    console.log("let's go")
  }
}
</script>

<style>
.challenge {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#company-logo {
  width: 120px;
}
</style>

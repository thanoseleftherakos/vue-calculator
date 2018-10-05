<template>
  <div id="app" class="page">
    <Calculator />
  </div>
</template>

<script>
import Calculator from './components/Calculator.vue'

export default {
  name: 'app',
  components: {
    Calculator
  }
}
</script>

<style lang="scss">
body, html {
    
}
body {
    font-family: 'Lato', sans-serif;
    font-size: 16px;
    margin: 0;
}

.page {
    height: 100vh;
    width: 100%;
    @media only screen and (min-width: 577px) {  
      min-height: 700px;
    }
    background: #ECE9E6; 
    background: linear-gradient(to top, #FFFFFF, #ECE9E6); 
}
</style>

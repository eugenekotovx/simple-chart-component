<template>
 <div class="app" id="app">
   <h1>Simple crypto chart component</h1>
   <chart/>
 </div>
</template>

<script>
import chart from '@/components/Chart'
export default {
  components: {
    chart
  },
}
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono&display=swap');
#app {
  font-family:'IBM Plex Mono', monospace;;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.app {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
h1 {
  margin-bottom: 50px;
}
body {

  background-color: #ecf0f3;
}
</style>

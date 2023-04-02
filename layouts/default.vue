<template>
  <div class="layout">
    <header class="header">Pixelpoint Coding Challenge</header>
    <div class="content">
      <Nuxt />
    </div>
    <footer class="footer">Footer</footer>
  </div>
</template>
<style>
.header{

  padding: 5%;
  height: 2vh;
}

.footer{
  background-color: black;
  height: 10vh;
}
body{
  margin: 0px;  
}
.content{
  min-height: 100vh;
  display: flex;
  padding: 5px;
}

</style>
<template>
  <div class="layout">
    <header class="header">Pixelpoint Coding Challenge</header>
    <div class="content">
      <Nuxt />
    </div>
    <footer class="footer"><NuxtLogo></NuxtLogo>Footer</footer>
  </div>
</template>
<style>
body{
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}
.header{
  background-color: #292d76;
  padding: 5%;
  height: 2vh;
  color: white;
  font-size: 2vw;
  font-weight: bold;
}
.footer{
  background-color: black;
  height: 10vh;
  text-align: center;
}
body{
  margin: 0px;  
}
.content{
  min-height: 100vh;
  display: flex;
  padding: 5px;
}
.nuxt-logo{
  padding-top: 20px;
  height: 50px;
}
</style>
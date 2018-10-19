<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
document.addEventListener('DOMContentLoaded',()=>{
  const html=document.querySelector('html')
  let fontSize=window.innerWidth/10
  fontSize=fontSize > 50 ? 50 : fontSize  //设置fontSize上限为50大于50则等于50，小于50等于自身
  html.style.fontSize=fontSize+'px'
})
</script>

<style>

</style>

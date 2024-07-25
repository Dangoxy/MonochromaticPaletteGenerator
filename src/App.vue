

<template>
  <div class="w-full heightFull flex flex-col">
    
    <router-view></router-view>
  </div>
</template>


<script setup>



</script>

<style scoped>
.heightFull{
  height: 100vh;
}

@media screen and (max-width:450px) {
  .heightFull{
    height: 100dvh;
  }
}

</style>
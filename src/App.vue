<template>
  <!-- <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div> -->
  <el-container id="app">
      <el-header>
        <navbar/>
        </el-header>
        <el-main>
          <div>
          <router-link to="/gacha">抽卡模拟器</router-link>
          <br>
          <router-link to="/unix-timestamp">unix时间戳转换</router-link>
          </div>
          <router-view></router-view>
          <!-- <gacha/> -->
          <!-- <unix-timestamp /> -->
        </el-main>
  </el-container>
</template>
<script>
// @ is an alias to /src
import Navbar from '@/components/navbar.vue'
// import UnixTimestamp from '@/components/unix-timestamp.vue'
// import Gacha from '@/components/gacha.vue'
export default {
  name: 'app',
  components: {
    Navbar
    // UnixTimestamp,
    // Gacha
  }
}
</script>
<style lang="scss">
// #app {
//   .el-main{
//     // height:1200px;
//   }
// }

// #nav {
//   padding: 30px;

//   a {
//     font-weight: bold;
//     color: #2c3e50;

//     &.router-link-exact-active {
//       color: #42b983;
//     }
//   }
// }
</style>

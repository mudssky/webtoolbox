<template>
  <div>
    <el-row>
      <el-input v-model="currentTimeStamp" :disabled="false"></el-input>
      <p>{{currentTimeStamp}}</p>
    </el-row>
  </div>
</template>
<script>
export default {
  name: 'unix-timestamp',
  props: {
    // currentTimeStamp: 0
  },
  data () {
    return {
      // 存放当前时间数据的变量
      currentTimeStamp: 0
    }
  },
  methods: {
  },
  mounted () {
    setInterval(function () {
      const currentDate = new Date()
      console.log(this.currentTimeStamp)
      this.currentTimeStamp = currentDate.getTime()
    }, 1000)
  }
}
</script>

<template>
  <div id="unix-timestamp">
      <el-row class="curTimestampRow" >
        <el-col :span=3>
            <span class="timestampLabel">当前的时间戳：</span>
        </el-col>
        <el-col :span=4>
            <span>{{currentTimestamp}}</span>
        </el-col>
        <el-col :span=3>
          <el-select v-model="curTimeOption" >
            <el-option
              v-for="item in timeOptions"
              :key="item.key"
              :label="item.label"
              :value="item.key">
            </el-option>
          </el-select>
        </el-col>
    </el-row>
  </div>
</template>
<script>
export default {
  name: 'unix-timestamp',
  props: {
    // currentTimestamp: Number
  },
  data () {
    return {
      currentTimestampMs: this.getcurrentTimestampMs(),
      // 存放当前时间数据的变量
      currentTimestamp: this.currentTimestampSecond,
      timeOptions: [
        {
          key: 'option1',
          label: '秒(s)'
        },
        {
          key: 'option2',
          label: '毫秒(ms)'
        }
      ],
      curTimeOption: 'option1'
    }
  },
  methods: {
    // 获取当前时间戳使用的函数
    getcurrentTimestampMs () {
      const currentDate = new Date()
      return currentDate.getTime()
    }
  },
  computed: {
    currentTimestampSecond: function () {
      // 毫秒数转换为秒数，并且四舍五入
      return Math.round(this.currentTimestampMs / 1000)
    }
  },
  watch: {
    currentTimestampMs: function (newVal) {
      if (this.curTimeOption === 'option1') {
        this.currentTimestamp = this.currentTimestampSecond
      } else {
        this.currentTimestamp = this.currentTimestampMs
      }
    }
  },
  mounted: function () {
    setInterval(() => {
      const currentDate = new Date()
      this.currentTimestampMs = currentDate.getTime()
    }, 1000)
  }
}
</script>
<style lang="scss">
$basefontSize : 20px;
#unix-timestamp{
  width: 1200px;
  margin: 0 auto;
  font-size: $basefontSize;
  span{
    vertical-align: middle;
    // line-height:20px;
  }
  // el-col {
  //   display: flex;
  //   justify-content: center;
  //   align-items: center;
  // }
  .curTimestampRow{
    height: 40px;
  }
}

// .timestampLabel{
//   font-size: $basefontSize;
// }
</style>

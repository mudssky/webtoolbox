<template>
  <div id="unix-timestamp">
    <el-row class="currentTimeRow">
      <span>当前的时间：</span>
        <span>{{currentTimeStr}}</span>
    </el-row>
      <el-row :gutter="20" class="curTimestampRow" >
        <el-col :span=4>
            <span class="timestampLabel">当前的时间戳：</span>
        </el-col>
        <el-col :span=4>
            <span>{{currentTimestamp}}</span>
        </el-col>
        <el-col :span=2 class="curTimeOption">
          <el-select v-model="curTimeOption" >
            <el-option
              v-for="item in timeOptions"
              :key="item.key"
              :label="item.label"
              :value="item.key">
            </el-option>
          </el-select>
        </el-col>
        <el-col :span=3 class="copyedTimeStamp">
           <el-button type="primary" v-on:click="copyTimeStamp">截取时间戳</el-button>
        </el-col>
    </el-row>
    <el-row :gutter="20" class="copyedTimeStampRow">
      <el-col :span=4>
        <span>截取的时间戳：</span>
      </el-col>
        <el-col :span=7>
            <el-input
            v-bind:maxlength="timeStampLength"
            v-bind:minlength="timeStampLength"
            placeholder="拷贝的内容"
            v-model="copyedTimeStamp"
            clearable></el-input>
        </el-col>
        <el-col :span=6>
          <el-date-picker
            v-model="copyedTimeStamp"
            type="datetime"
            value-format="timestamp"
            placeholder="选择日期时间">
          </el-date-picker>
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
      curTimeOption: 'option1',
      // currentTimeStr: new Date(currentTimestampMs).toString()
      copyedTimeStamp: 1610357992
    }
  },
  methods: {
    // 获取当前时间戳使用的函数
    getcurrentTimestampMs () {
      const currentDate = new Date()
      return currentDate.getTime()
    },
    copyTimeStamp () {
      this.copyedTimeStamp = this.currentTimestamp
    }
    // fixInputTimeStamp () {
    //   if (this.copyedTimeStamp === '') {
    //     return
    //   }
    //   const timeStampRe = new RegExp(/^[1-9]\d*$/)
    //   if (timeStampRe.test(this.copyedTimeStamp)) {
    //     if (this.copyedTimeStamp.length > this.timeStampLength) {
    //       this.copyedTimeStamp = this.copyedTimeStamp.substring(0, this.timestampLength)
    //     }
    //   } else {
    //     this.$message(
    //       {
    //         showClose: true,
    //         message: '请输入格式正确的时间戳',
    //         type: 'warning'
    //       })
    //     this.copyedTimeStamp = ''
    //   }
    // }
  },
  computed: {
    currentTimestampSecond: function () {
      // 毫秒数转换为秒数，并且四舍五入
      return Math.round(this.currentTimestampMs / 1000)
    },
    currentTimeStr: function () {
      // return new Date(this.currentTimestampMs).toString()
      return new Date(this.currentTimestampMs).toLocaleString()
    },
    timeStampLength: function () {
      // 判断当前的单位选项是毫秒还是秒，返回当前时间戳的长度
      return this.curTimeOption === 'option1' ? 10 : 13
    }
  },
  watch: {
    currentTimestampMs: function (newVal) {
      if (this.curTimeOption === 'option1') {
        this.currentTimestamp = this.currentTimestampSecond
      } else {
        this.currentTimestamp = this.currentTimestampMs
      }
    },
    copyedTimeStamp: function (newVal) {
      if (newVal === '') {
        return
      }
      // const copyedTimeStampStr = String(this.copyedTimeStamp)
      const timeStampStr = String(newVal)
      const timeStampRe = new RegExp(/^[1-9]\d*$/)
      if (timeStampRe.test(timeStampStr)) {
        if (timeStampStr.length > this.timeStampLength) {
          this.copyedTimeStamp = parseInt(timeStampStr.substring(0, this.timestampLength))
          this.$message(this.copyedTimeStamp + '')
        }
      } else {
        this.$message(
          {
            showClose: true,
            message: '请输入格式正确的时间戳',
            type: 'warning'
          })
        this.copyedTimeStamp = ''
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
$rowHeight:40px;

#unix-timestamp{
  width: 1200px;
  margin: 0 auto;
  font-size: $basefontSize;
  span{
    vertical-align: top;
    // line-height:20px;
  }
  // el-col {·
  //   display: flex;
  //   justify-content: center;
  //   align-items: center;
  // }
  .currentTimeRow{
    height: $rowHeight;
  }
  .curTimestampRow{
    height: $rowHeight;
  }
  .copyedTimeStampRow{
    height: $rowHeight;
  }
  .curTimeOption{
    width: 120px;
  }
}

// .timestampLabel{
//   font-size: $basefontSize;
// }
</style>

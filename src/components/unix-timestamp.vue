<template>
  <div id="unix-timestamp">
    <el-row  class="currentTimeRow">
        <span>当前的时间：</span>
        <span>{{currentTimeStr}}</span>
    </el-row>
      <el-row :gutter="20" class="curTimestampRow" >
        <el-col :span="4">
            <span class="timestampLabel">当前的时间戳：</span>
        </el-col>
        <el-col :span="8">
            <span>{{currentTimestamp}}</span>
        </el-col>
        <el-col :span="4" class="curTimeOption">
          <el-select v-model="curTimeOption" >
            <el-option
              v-for="item in timeOptions"
              :key="item.key"
              :label="item.label"
              :value="item.key">
            </el-option>
          </el-select>
        </el-col>
        <el-col :span="3" class="copyedTimeStamp">
           <el-button type="primary" v-on:click="copyTimeStamp">截取时间戳</el-button>
        </el-col>
    </el-row>
    <el-row :gutter="20" class="copyedTimeStampRow">
      <el-col :span="6">
        <span>截取的时间戳和对应的时间</span>
      </el-col>
        <el-col :span="7">
            <el-input
            v-bind:maxlength="timeStampLength"
            v-bind:minlength="timeStampLength"
            type="number"
            placeholder="拷贝的内容"
            v-model="copyedTimeStamp"
            v-on:change="validateAndConvert"
            clearable></el-input>
        </el-col>
        <el-col :span="6">
          <el-date-picker
            v-model="pickedTimeStamp"
            type="datetime"
            value-format="timestamp"
            placeholder="选择日期时间"
            v-on:change="updateTimeStamp"
            >
          </el-date-picker>
        </el-col>
    </el-row>
    <el-row class="howToRow">
      <div>如何在不同编程语言中获取现在的Unix时间戳(Unix timestamp)？</div>
      <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="name"
        label="语言"
        width="180">
      </el-table-column>
      <el-table-column
        prop="desp"
        label="说明"
        width="560">
      </el-table-column>
    </el-table>
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
      copyedTimeStamp: 1610357992,
      // copyedTimeStampMs: 0,
      // 这边的精度是毫秒，所以用这个属性作为基准值
      pickedTimeStamp: 1610357992000,

      // 忽略tableData的eslint检查，因为描述里面要用到反引号，使用js的模板字符串语法，所以会触发eslint的引号检查
      /* eslint-disable */
      tableData: [{
        name: 'JavaScript',
        desp: 'new Date().getTime() ; new Date().toLocaleString()'
      },
      {
        name: 'golang',
        desp: 'time.Now().Unix()'
      }
      ]
      /* eslint-disable */
      // convertedTimeStr: ''
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
      this.validateAndConvert()
    },
    validateAndConvert () {
      // js进行数字的赋值的时候，copyedTimeStamp会变成number类型
      if (typeof this.copyedTimeStamp === 'number') {
        if (this.curTimeOption === 'option1') {
          this.pickedTimeStamp = this.copyedTimeStamp * 1000
        } else if (this.curTimeOption === 'option2') {
          this.pickedTimeStamp = this.copyedTimeStamp
        }
        // 如果此时数据不是number类型，因为输入框输入的默认是text，是string类型
        // 把input标签的type改为number只是浏览器端多了一步验证，让你无法输入或者拷贝数字以外的字符串，
        // 但是双向绑定传递过来的仍然是字符串
      } else if (typeof this.copyedTimeStamp === 'string') {
        const timeStampRe = new RegExp(/^[1-9]\d*$/)
        if (timeStampRe.test(this.copyedTimeStamp)) {
          const copyedTimeStampNum = parseInt(this.copyedTimeStamp)
          // console.log(this.copyedTimeStamp)
          if (this.curTimeOption === 'option1') {
            this.pickedTimeStamp = copyedTimeStampNum * 1000
          } else if (this.curTimeOption === 'option2') {
            this.pickedTimeStamp = copyedTimeStampNum
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
    updateTimeStamp () {
      this.copyedTimeStamp = (this.curTimeOption === 'option1' ? this.pickedTimeStamp / 1000 : this.pickedTimeStamp)
    }
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
    },
    convertedTimeStr: function () {
      // const newDate = new Date(this.copyedTimeStamp)
      return new Date(this.copyedTimeStamp).toLocaleString()
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
    curTimeOption: function (newVal) {
      if (newVal === 'option1') {
        this.copyedTimeStamp = Math.round(this.copyedTimeStamp / 1000)
      } else {
        this.copyedTimeStamp = this.copyedTimeStamp * 1000
      }
    },
    pickedTimeStamp: function (newVal) {
      if (this.curTimeOption === 'option1') {
        this.copyedTimeStamp = newVal / 1000
      } else {
        this.copyedTimeStamp = newVal
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
  height:800px;
  margin: 0 auto;
  font-size: $basefontSize;
  span{
    vertical-align: top;
    // line-height:20px;
  }
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
  .howToRow{
    height: $rowHeight;
    color: blue;
    margin-top: 20px;
    // overflow: visible;
  }
}
</style>

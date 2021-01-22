<template>
  <div id="gacha">
    <el-row>
      <div class="gachaPool-label">
        目前卡池中的卡牌总数是{{cardList.length}}
      </div>
    </el-row>
    <el-row :gutter="5" class="gachaPool">
      <el-col :span="2" v-bind:key="card.id" v-for="card in cardList">
        <el-tooltip  placement="top">
          <div slot="content">{{card}}</div>
          <el-tag :color="card.color" class="gachaPool-tag">{{card.name}}</el-tag>
         </el-tooltip>
      </el-col>
    </el-row>
    <el-row>
      <div>抽卡统计</div>
      <!-- <table class="gahcaReport">
       <tr>
         <td>SSR</td>
         <td>SR</td>
         <td>N</td>
         <td>R</td>
         <td>抽取次数</td>
       </tr>
       <tr>
         <td>{{rarityCount['SSR']}}</td>
         <td>{{rarityCount['SR']}}</td>
         <td>{{rarityCount['N']}}</td>
         <td>{{rarityCount['R']}}</td>
         <td>{{resultList.length}}</td>
       </tr>
      </table> -->
      <!-- <el-col :key="rarity.id" v-for="rarity in rarityList">
        <div>抽到稀有度为{{rarity.name}}的卡片数量为{{rarityCount[rarity.name]}}</div>
      </el-col> -->
    </el-row>
    <el-row>
      <el-col :span="24">
        <div>
        {{testRarityCount}}
        <el-button type="primary" v-on:click="testGachaRarity(10000,0)">抽取稀有度</el-button>
        <el-button type="primary" v-on:click="testAddCard">添加卡片</el-button>
        <el-button type="primary" v-on:click="gachaTenth">抽取10连</el-button>
        <el-button type="primary" v-on:click="gachaOnce">抽取1次</el-button>
        </div>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="12">
        <div>
        <el-table
        :data="resultList"
        style="width: 100%"
        :default-sort = "{prop: 'index', order: 'descending'}"
        :row-class-name="tableRowClassName"
        >
        <el-table-column
          label="序号"
          prop="index"
          sortable
          width="180">
        </el-table-column>
        <el-table-column
          prop="name"
          label="名字"
          sortable
          width="180">
        </el-table-column>
        <el-table-column
          prop="rarity"
          label="稀有度">
        </el-table-column>
        <el-table-column
          prop="rarityP"
          sortable
          label="稀有度概率">
        </el-table-column>
        </el-table>
      </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
/*
卡池设计是这样的：

抽卡逻辑的流程有以下两种处理方式：
方法一：先抽取稀有度，再抽取指定稀有度的卡池
1.首先有很多种稀有度，加起来的概率是100%，先确定卡片的稀有度。
2.抽取指定稀有度的卡池，如果卡池有概率提升，那么对应提升的卡片的概率增加，其余没有pickup的卡片均分剩下的概率（所以说pickup的概率加起来必须小于该稀有度总的概率）
方法二： 直接抽取指定的卡池
卡池中所有卡的概率加起来是100%，然后按照每张卡的概率划分范围，生成随机浮点数，生成的浮点数落在哪张卡的范围，就是抽到哪张卡。
第二种方法就少了一次随机数计算，也比较简单，但是问题在于每次加入新卡以后，概率需要重新计算。
*/
export default {
  name: 'gacha',
  data () {
    return {
      testRarityCount: {
        SSR: 0,
        SR: 0,
        N: 0,
        R: 0
      },
      rarityCount: {
        SSR: 0,
        SR: 0,
        N: 0,
        R: 0
      },
      // 稀有度列表，所有稀有度的概率加起来必须为1.
      rarityList: [
        // {
        //   id: 1,
        //   name: 'UR',
        //   // cnName: '',
        //   color: 'niji',
        //   probability: 0.012
        // },
        {
          id: 2,
          name: 'SSR',
          color: 'gold',
          probability: 0.07
        },
        {
          id: 3,
          name: 'SR',
          color: 'purple',
          probability: 0.12
        },
        {
          id: 4,
          name: 'R',
          color: 'blue',
          probability: 0.26
        },
        {
          id: 5,
          name: 'N',
          color: 'gray',
          probability: 0.55
        }
      ],
      // 存放所有卡片对象
      cardList: [],
      // 存放抽卡结果的列表，按抽取顺序进行存放，后抽取的push到列表的尾部
      resultList: [],
      // 获得概率提升的卡的概率，用于构建up池
      pickupP: 0.02
    }
  },
  methods: {
    // 检查稀有度列表是否有误，即所有稀有度的概率和必须为1.
    validateRarityList () {
      if (this.totalRarityProbability === 1) {
        return true
      }
      return false
    },
    // 用于生成[0,1)范围的随机浮点数,
    randomFunc () {
      return Math.random()
    },
    // 生成[startNum,endNum)之间的随机整数
    randomInt (startNum, endNum) {
      return this.randomFunc() * (endNum - startNum) + startNum
    },
    // 01.抽取的两个步骤，第一步是确定卡片的稀有度
    gachaRarity () {
      if (!this.validateRarityList()) {
        alert('稀有度列表有错误，所有稀有度的概率加起来必须为1')
        throw Error('稀有度列表有错误，所有稀有度的概率加起来必须为1')
      }
      // 生成[0,1)范围的随机数
      // 划分0-1的区间，随机数生成在0-1的概率是均匀的，所以划分区间的范围和概率是正比的。
      // 判断生成的随机数出现哪个区间，就说明抽到那哪个稀有度的卡。
      // 这边采用 1的初始范围下探， 0.93-1 就是ssr ，依次类推。
      let range = 1
      const randomNum = this.randomFunc()
      for (let i = 0; i < this.rarityList.length; i++) {
        range -= this.rarityList[i].probability
        if (randomNum >= range) {
          // console.log(this.rarityList[i].name)
          return this.rarityList[i]
        }
      }
    },
    gachaFromRarity (rarity) {
      // console.log(this.rarityCardList[rarity])
      const gachaPool = this.rarityCardList[rarity]
      // console.log(gachaPool)
      const item = this.gachaFromPool(gachaPool)
      console.log(item)
    },
    // 从卡池抽卡，卡池是一个列表包含所有卡片对象，并且所有卡片对象必须有一个出现概率属性。
    gachaFromPool (gachaPool) {
      let range = 1
      const randomNum = this.randomFunc()
      // console.log(gachaPool.length)
      // console.log(randomNum)
      for (let i = 0; i < gachaPool.length; i++) {
        // console.log(gachaPool[i].probability)
        range -= gachaPool[i].probability
        if (randomNum >= range) {
          // console.log(this.rarityList[i].name)
          // console.log(gachaPool[i])
          return gachaPool[i]
        }
      }
    },
    // 检查卡池所有项目的出现概率之和是否等于1,gachaPool每个对象必须有一个概率属性
    checkPool (gachaPool) {
      let probabilitySum = 0
      gachaPool.forEach((item) => {
        probabilitySum += item.probability
      })
      if (probabilitySum === 1) {
        return true
      }
      return false
    },
    gachaOnce () {
      const item = this.gachaFromPool(this.cardList)
      // console.log(item)
      // const generateTime = new Date().getTime()
      // item.generateTime = generateTime
      this.resultList.push(item)
      // return
    },
    gachaTenth () {
      for (let i = 0; i < 10; i++) {
        this.gachaOnce()
      }
    },
    testGachaRarity (times, delay) {
      if (delay <= 0) {
        for (let i = 0; i < times; i++) {
          const rarityObj = this.gachaRarity()
          this.testRarityCount[rarityObj.name] += 1
        }
        return
      }
      let count = 0
      const gachaInterval = setInterval(() => {
        const rarityObj = this.gachaRarity()
        this.testRarityCount[rarityObj.name] += 1
        // console.log(this.testRarityCount[rarityObj.name])
        count += 1
        if (count >= times) {
          clearInterval(gachaInterval)
        }
      }, delay)
    },
    testAddCard () {
      let increasedID = 0
      // console.log('rarityList length:', this.rarityList.length)
      for (let i = 0; i < this.rarityList.length; i++) {
        const range = Math.round(this.rarityList[i].probability * 100)
        for (let j = 0; j < range; j++) {
          increasedID += 1
          this.cardList.push({
            id: increasedID,
            name: increasedID + '',
            color: this.rarityList[i].color,
            rarity: this.rarityList[i].name,
            rarityP: this.rarityList[i].probability
          })
        }
      }
      this.calculateP()
    },
    // 添加完卡片后，按稀有度存放卡片的列表也完成了，需要根据这个稳定的数据来计算每张卡的概率
    calculateP () {
      this.cardList.forEach((card) => {
        const cardRarityCount = this.rarityCardList[card.rarity].length
        const totalP = card.rarityP
        card.probability = totalP / cardRarityCount
      })
    },
    // 根据卡片的稀有度，显示不同的颜色
    tableRowClassName (row, rowIndex) {
      const rarityClass = {
        R: 'row-blue',
        SR: 'row-purple',
        SSR: 'row-gold',
        N: 'row-white'
      }
      // console.log(row.row.rarity)
      // console.log(row)
      return rarityClass[row.row.rarity]
    }
  },
  watch: {
    // 监听数组长度,统计各个稀有度的卡片数据
    // 由于elementui 的table没有提供记录每条记录在原数组的下标的方法，所以，我采用手动添加index的属性的方式
    // 监听数组的长度，只有数组长度增加的时候进行执行添加index属性的操作（因为抽卡只有添加和删除两种操作，删除操作不会改变已经有的index，只要不执行替换操作就不会出现BUG）
    'resultList.length' (newVal, oldVal) {
      const increaseNum = newVal - oldVal
      if (increaseNum > 0) {
        for (let i = oldVal; i < newVal; i++) {
          this.resultList[i].index = i
          // console.log(this.resultList[i].index)
          // const card = this.resultList[i - 1]
          // this.rarityCount[card.rarity]++
        }
      }
      console.log('new', newVal)
      console.log('old', oldVal)
    }
  },
  computed: {
    // 稀有度概率总和，当稀有度列表发生变化的时候，会重新计算新的稀有度概率总和
    totalRarityProbability () {
      let total = 0
      this.rarityList.forEach((curItem) => {
        total += curItem.probability
      })
      return total
    },
    // 按照稀有度分类卡牌，每个稀有度的卡牌对象都放到一个列表里
    rarityCardList () {
      const res = {}
      this.cardList.forEach((item) => {
        if (item.rarity in res) {
          res[item.rarity].push(item)
        } else {
          res[item.rarity] = []
          res[item.rarity].push(item)
        }
      })
      return res
    }
    // rarityCount () {
    //   const res = {
    //     SSR: 0,
    //     SR: 0,
    //     N: 0,
    //     R: 0
    //   }
    //   this.resultList.forEach((item) => {
    //     res[item.rarity]++
    //   })
    //   return res
    // }
  },
  beforeMount () {
    this.testAddCard()
  }
}
</script>
<style lang="scss" >
.row-blue{
  background: lightblue !important;
  // color:yellow;
}
.row-purple{
  background:rgb(191, 130, 240) !important;
}
.row-white{
  background:white !important;
}
.row-gold{
  background:gold !important;
}
#gacha{
    width:1200px;
    margin: 0 auto;
    .el-row{
      width:100%;
    }
    // .gachaReport{
    //   border-collapse:collapse;
    //   border:1px solid black !important;
    // }
    // .gachaReport td{
    //   // border:1px solid black;
    //   border:1px solid black !important;
    // }
    //  .gachaReport tr{
    //   // border:1px solid black;
    //   border:1px solid black !important;
    // }
    .gachaPool{
      widows:100%;
      min-height:150px;
      background-color:lightcyan;
    }
    .gachaPool-tag{
      color:white;
    }
    .gachaPool-label{
      font-size:20px;
    }
}
</style>

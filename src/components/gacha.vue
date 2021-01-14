<template>
  <el-container id="gacha">
      {{validateRarityList()}}
      <el-button type="primary" v-on:click="gachaRarity">抽取稀有度</el-button>
  </el-container>
</template>

<script>
/*
卡池设计是这样的：

抽卡逻辑的流程是这样的：
1.首先有很多种稀有度，加起来的概率是100%，先确定卡片的稀有度。
2.抽取指定稀有度的卡池，如果卡池有概率提升，那么对应提升的卡片的概率增加，其余没有pickup的卡片均分剩下的概率（所以说pickup的概率加起来必须小于该稀有度总的概率）
*/
export default {
  name: 'gacha',
  data () {
    return {
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
      resultList: []
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
      // const randomNum = this.randomFunc()
    },
    gachaFromRarity (rarity) {

    },
    gachaOnce () {
      return {

      }
    },
    gachaTenth () {
      return []
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
    }
  }
}
</script>
<style>
</style>

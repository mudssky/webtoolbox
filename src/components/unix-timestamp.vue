<template>
  <div>
    <el-row>
      <el-input v-model="currentTimeStamp" :disabled="true"> </el-input>
    </el-row>
  </div>
</template>
<script>
export default {
  name: "unix-timestamp",
  data() {
    return {
      currentTimeStamp: function () {
        const date = new Date();
        return date.toTimeString();
      },
    };
  },
  methods: {
    handleSelect(key, keyPath) {
      console.log(key, keyPath);
    },
  },
};
</script>
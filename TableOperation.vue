<template>
  <el-table-column label="操作" :width="width">
    <template slot-scope="scope">
      <template v-for="(o, index) in operations">
        <el-popconfirm v-if="o.isConfirm" :key="o.label" title="确定删除吗？" @onConfirm="o.onClick(scope.row)">
          <span slot="reference" style="color: #ffd04b">删除</span>
        </el-popconfirm>
        <span v-else :key="o.label" @click="o.onClick(scope.row)" style="color: #ffd04b">{{ o.label }}</span>
        <el-divider v-if="index !== operations.length - 1" :key="o.label" direction="vertical"></el-divider>
      </template>
    </template>
  </el-table-column>
</template>

<script>
export default {
  name: 'TableOperation',
  props: {
    operations: Array,
    width: {
      type: Number,
      default: 150,
    },
  },
};
</script>

<style scoped>
span {
  color: #1890ef;
  cursor: pointer;
}
span:hover {
  text-decoration: underline;
}
</style>

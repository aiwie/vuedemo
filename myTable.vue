<template>
  <el-table
    :data="tableData"
    @selection-change="handleSelectionChange"
    stripe
    style="width: 100%"
    height="250"
    :header-cell-class-name="$disabledCheck"
    ref="multipleTable"
  >
    <el-table-column v-if="configlist.isShowSelect" type="selection">
    </el-table-column>
    <el-table-column type="index" label="序号"> </el-table-column>
    <template v-for="item in configlist.tableHeader">
      <el-table-column
        :prop="item.prop"
        :label="item.label"
        :key="item.lable"
        :width="item.width"
        v-if="!item.isCustom"
      >
      </el-table-column>
      <el-table-column
        :label="item.label"
        :key="item.lable"
        :width="item.width"
        v-if="item.isCustom"
      >
        <template slot-scope="scope">
          <slot :name="item.prop" :row="scope.row"></slot>
        </template>
      </el-table-column>
    </template>
  </el-table>


    <!-- 调用组件时 插槽使用方法 -->
    <!-- <template  v-slot:faciCategory="scope">
            {{ scope.row.faciCategory }}
          </template> -->

</template>

<script>
export default {
  name: 'mytable',
  props: {
    tableData: {
      type: Array
    },
    tableConfig: {
      type: Object
    }
  },
  computed: {
    configlist() {
      return Object.assign(
        {
          isShowSelect: true
          //...
        },
        this.tableConfig
      )
    }
  },
  methods: {
    handleSelectionChange(e) {
      this.$emit('setMultipleSelection',e)
      if (e.length > 1) {
        this.$refs.multipleTable.clearSelection()
        this.$refs.multipleTable.toggleRowSelection(e.pop())
      }
    }
  }
}

</script>

<style>
</style>





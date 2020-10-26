<template>
  <div class="common_table">
    <el-table
      border
      :data="data"
      style="width: 100%; margin-top: 10px"
      :header-cell-style="{
        background: '#909399',
        color: '#fff',
      }"
      :max-height="maxHeight"
      v-loading="loading"
      @row-dblclick="handleRowDblClick"
    >
      <el-table-column
        v-for="item in column"
        :key="item.value"
        :type="item.value"
        :prop="item.value"
        :label="item.label"
        :width="item.width"
        :fixed="item.fixed"
      >
        <template slot-scope="scope">
          <template v-if="isCustom">
            <slot :item="item" :scope="scope"></slot>
          </template>
          <span v-else>{{ scope.row[item.value] }}</span>
        </template>
      </el-table-column>
      <table-operation v-if="operations.length > 0" :operations="operations"></table-operation>
    </el-table>
    <el-pagination
      v-if="isShowPagination"
      background
      layout="pager, sizes"
      :page-sizes="[13, 26, 30, 50]"
      :page-size="pageSize"
      :total="originTableData.length"
      :current-page.sync="currentPage"
      :hide-on-single-page="hideOnSinglePage"
      @current-change="handleChangeCurrentPage"
      @size-change="handleSizeChange"
    >
    </el-pagination>
  </div>
</template>

<script>
import { reactive, toRefs, computed, watch, onBeforeMount } from '@vue/composition-api';

import TableOperation from './TableOperation';

export default {
  props: {
    tableData: {
      type: Array,
      default: () => [],
    },
    loading: {
      type: Boolean,
      default: false,
    },
    column: {
      type: Array,
      default: () => [],
    },
    operations: {
      type: Array,
      default: () => [],
    },
    propsPageSize: {
      type: Number,
      default: 13,
    },
    isCustom: {
      type: Boolean,
      default: false,
    },
    maxHeight: {
      type: Number,
      default: null,
    },
    isShowPagination: {
      type: Boolean,
      default: true,
    },
    hideOnSinglePage: {
      type: Boolean,
      default: true,
    },
  },
  components: { TableOperation },
  setup(props, { emit }) {
    const state = reactive({
      originTableData: [],
      data: [],
      currentPage: 1,
      pageSize: props.propsPageSize,
    });
    watch(
      () => props.tableData,
      value => {
        state.originTableData = value;
        state.data = value.slice(0, state.pageSize);
      }
    );
    watch(
      () => state.pageSize,
      () => {
        state.currentPage = 1;
        getTableList();
      }
    );
    watch(
      () => state.currentPage,
      () => getTableList()
    );

    const getTableList = () => {
      const { originTableData, currentPage, pageSize } = state;
      state.data = state.originTableData.slice((currentPage - 1) * pageSize, currentPage * pageSize);
    };

    const handleRowDblClick = (row, column, event) => emit('on-row-db-click', { row, column, event });

    const handleChangeCurrentPage = currentPage => emit('on-change-current-page', currentPage);

    const handleSizeChange = currentPageSize => (state.pageSize = currentPageSize);

    return {
      ...toRefs(state),
      handleRowDblClick,
      handleSizeChange,
      handleChangeCurrentPage,
    };
  },
};
</script>

<style>
.el-table,
.el-table td {
  background: #909399;
  color: #fff;
}
.el-table__empty-text {
  color: #fff;
}
.el-table__body tr.hover-row.current-row > td,
.el-table__body tr.hover-row.el-table__row--striped.current-row > td,
.el-table__body tr.hover-row.el-table__row--striped > td,
.el-table__body tr.hover-row > td {
  background: #545c64;
}
.el-loading-mask {
  background: #545c64;
}
.el-pagination {
  text-align: right;
  margin-top: 10px;
}
.el-pagination button:disabled,
.el-pagination .btn-next,
.el-pagination .btn-prev,
.el-pager li {
  background-color: #909399;
}
</style>

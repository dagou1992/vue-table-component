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
      @filter-change="handleChangeFilter"
    >
      <el-table-column
        v-for="item in column"
        :key="item.value"
        :type="item.value"
        :prop="item.value"
        :label="item.label"
        :width="item.width"
        :fixed="item.fixed"
        :sortable="item.sortable"
        :column-key="item.value"
        :filters="item.filter && getFilters(item.value)"
        :filter-method="item.filter && getFilterData"
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
      :page-sizes="pageSizes"
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
      filterObject: {},
      paginationTotal: 0,
    });
    watch(
      () => props.tableData,
      value => {
        state.originTableData = value;
        state.paginationTotal = value.length;
        state.data = value.slice((state.currentPage - 1) * state.pageSize, state.pageSize * state.currentPage);
        state.filterObject = {};
        table.value.clearFilter();
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

    const pageSizes = computed(() => [props.propsPageSize, props.propsPageSize * 2, props.propsPageSize * 3, props.propsPageSize * 4])

    const getTableList = () => {
      const { originTableData, currentPage, pageSize } = state;
      state.data = state.originTableData.slice((currentPage - 1) * pageSize, currentPage * pageSize);
    };

    const handleRowDblClick = (row, column, event) => emit('on-row-db-click', { row, column, event });

    const handleChangeCurrentPage = currentPage => emit('on-change-current-page', currentPage);

    const handleSizeChange = currentPageSize => (state.pageSize = currentPageSize);
    
    const getFilters = value =>
      [...new Set(state.data.map(item => item[value]))].filter(item => item).map(item => ({ text: item, value: item }));

    const getFilterData = (value, row, column) => {
      const property = column['property'];
      return row[property] === value;
    };

    const handleChangeFilter = value => {
      state.filterObject = { ...state.filterObject, ...value };
      let originData = JSON.parse(JSON.stringify(state.originTableData));
      Object.keys(state.filterObject).forEach(key => {
        if (state.filterObject[key].length > 0) {
          originData = originData.filter(item => state.filterObject[key].includes(item[key]));
        }
      });
      state.data = originData.slice((state.currentPage - 1) * state.pageSize, state.pageSize * state.currentPage);
      state.paginationTotal = originData.length;
    };

    return {
      ...toRefs(state),
      pageSizes,
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

# vue-table-component
基于Element封装的表格组件


### 可选参数：

tableData(Array)：表格数据  
loading(Boolean)：表格加载动画  
column(Array)： 表头数据  
operations(Array)：表格内嵌的操作选项列表  
propsPageSize(Number)：每页显示条目个数  
isCustom(Boolean)：是否显示表格单元格个性化设置  
maxHeight(Number)：表格最大高度，超出部分会显示滑动条  
isShowPagination(Boolean):  是否显示页码  
hideOnSinglePage(Boolean): 只有一页时是否隐藏  


### 参考示例：

tableData:  
```javascript
[
    { index: 1,  username: 'jinbangqiang', role: 1 },
    ...
]
```  

column:  
```javascript
[
    { label: '序号', value: 'index', width: 100 },
    { label: '用户名', value: 'username', fixed: true },
    { label: '角色', value: 'role' },
],
```  

operations:
```javascript
operations: [
    {
      label: '编辑',
      onClick: value => handleToEdit(value),
    },
    {
      label: '删除',
      isConfirm: true, // 是否需要确定框
      onClick: value => handleToDelete(value),
    },
],
```

isCustom:
```javascript
<common-table
  :table-data="data"
  :column="column"
  :loading="loading"
  :operations="operations"
  :is-custom="true"
  :hide-on-single-page="false"
>
  <template #default="{ scope, item }">
    <span v-if="item.label === '角色'">{{ scope.row[item.value] === 1 ? '管理员' : '普通用户' }}</span>
    <span v-else>{{ scope.row[item.value] }}</span>
  </template>
</common-table>
```

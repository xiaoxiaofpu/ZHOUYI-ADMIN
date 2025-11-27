<template>
  <div class="comprehensive-example">
    <div class="page-header">
      <h1>综合示例</h1>
      <p>本页面展示了系统中各种组件的综合应用</p>
    </div>

    <div class="content-wrapper">
      <!-- 搜索与过滤区域 -->
      <div class="section-group">
        <div class="example-section">
          <h2>搜索面板</h2>
          <SearchPanel :searchConfig="searchConfig" @search="handleSearch" @reset="handleReset" />
        </div>

        <div class="example-section">
          <h2>表格查询表单</h2>
          <ZyTableQueryForm :formItems="queryFormItems" @search="handleQuerySearch" @reset="handleQueryReset" />
        </div>

        <div class="example-section">
          <h2>表格过滤器</h2>
          <ZyTableFilter :filters="tableFilters" @filter-change="handleFilterChange" />
        </div>
      </div>

      <!-- 数据表格区域 -->
      <div class="example-section">
        <h2>数据表格</h2>
        <ZyElTable
          :table-data="tableData"
          :columns="tableColumns"
          :pagination="pagination"
          :loading="tableLoading"
          @pagination-change="handlePaginationChange"
          @row-click="handleRowClick"
        >
          <template #action="{ row }">
            <ZyTableButtons
              :row="row"
              :buttons="tableButtons"
              @edit="handleEdit"
              @delete="handleDelete"
              @view="handleView"
            />
          </template>
        </ZyElTable>
      </div>

      <!-- 图表与媒体区域 -->
      <div class="section-group">
        <div class="example-section chart-section">
          <h2>图表展示</h2>
          <div class="chart-wrapper">
            <ZyEcharts :options="chartOptions" style="height: 300px;" />
          </div>
        </div>

        <div class="example-section">
          <h2>图片列表</h2>
          <ZyImgList :imgList="imgList" @delete="handleImgDelete" />
        </div>
      </div>

      <!-- 上传与编辑区域 -->
      <div class="section-group">
        <div class="example-section">
          <h2>文件上传</h2>
          <ZyUpload
            :action="uploadAction"
            :file-list="fileList"
            @upload-success="handleUploadSuccess"
            @upload-error="handleUploadError"
            @file-remove="handleFileRemove"
          />
        </div>

        <div class="example-section">
          <h2>富文本编辑器</h2>
          <WangEditor v-model="editorContent" @change="handleEditorChange" />
        </div>

        <div class="example-section">
          <h2>图标选择器</h2>
          <IconSelect v-model="selectedIcon" @change="handleIconChange" />
          <div class="selected-icon" v-if="selectedIcon">
            <el-icon :size="32"><component :is="selectedIcon" /></el-icon>
            <span>{{ selectedIcon }}</span>
          </div>
        </div>
      </div>

      <!-- 对话框示例 -->
      <div class="example-section">
        <h2>对话框</h2>
        <el-button type="primary" @click="showDialog = true">打开对话框</el-button>
        <ZyElDialog
          v-model="showDialog"
          title="示例对话框"
          width="500px"
          @confirm="handleDialogConfirm"
          @cancel="handleDialogCancel"
        >
          <div>
            <el-form :model="formData" label-width="80px">
              <el-form-item label="姓名">
                <el-input v-model="formData.name" />
              </el-form-item>
              <el-form-item label="邮箱">
                <el-input v-model="formData.email" />
              </el-form-item>
            </el-form>
          </div>
        </ZyElDialog>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import SearchPanel from '@/components/SearchPanel/index.vue'
import ZyElTable from '@/components/ZyElTable/index.vue'
import ZyEcharts from '@/components/ZyEcharts/index.vue'
import ZyUpload from '@/components/ZyUpload/index.vue'
import ZyElDialog from '@/components/ZyElDialog/index.vue'
import ZyTableQueryForm from '@/components/ZyTableQueryForm/index.vue'
import ZyTableFilter from '@/components/ZyTableFilter/index.vue'
import ZyTableButtons from '@/components/ZyTableButtons/index.vue'
import ZyImgList from '@/components/ZyImgList/index.vue'
import WangEditor from '@/components/WangEditor/index.vue'
import IconSelect from '@/components/IconSelect/index.vue'

// 搜索配置
const searchConfig = ref({
  searchList: [
    { label: '姓名', prop: 'name', type: 'input' },
    { label: '状态', prop: 'status', type: 'select', options: [{ label: '启用', value: 1 }, { label: '禁用', value: 0 }] },
    { label: '创建时间', prop: 'createTime', type: 'date-picker', valueFormat: 'YYYY-MM-DD' }
  ]
})

// 表格查询表单配置 - 确保表单能正确显示
const queryFormItems = ref([
  {
    label: '姓名',
    prop: 'name',
    type: 'input',
    placeholder: '请输入姓名',
    span: 8
  },
  {
    label: '邮箱',
    prop: 'email',
    type: 'input',
    placeholder: '请输入邮箱',
    span: 8
  },
  {
    label: '状态',
    prop: 'status',
    type: 'select',
    options: [
      { label: '全部', value: '' },
      { label: '启用', value: 1 },
      { label: '禁用', value: 0 }
    ],
    span: 8
  }
])

// 表格过滤器配置
const tableFilters = ref([
  { label: '状态', prop: 'status', type: 'select', options: [{ label: '全部', value: '' }, { label: '启用', value: 1 }, { label: '禁用', value: 0 }] },
  { label: '创建时间', prop: 'createTime', type: 'date-range', valueFormat: 'YYYY-MM-DD' }
])

// 表格按钮配置
const tableButtons = ref([
  { type: 'edit', label: '编辑', icon: 'Edit' },
  { type: 'delete', label: '删除', icon: 'Delete', confirm: true },
  { type: 'view', label: '查看', icon: 'View' }
])

// 表格数据
const tableData = ref([
  { id: 1, name: '张三', email: 'zhangsan@example.com', status: 1, createTime: '2023-01-01' },
  { id: 2, name: '李四', email: 'lisi@example.com', status: 1, createTime: '2023-01-02' },
  { id: 3, name: '王五', email: 'wangwu@example.com', status: 0, createTime: '2023-01-03' }
])

// 表格列配置
const tableColumns = ref([
  { label: 'ID', prop: 'id', width: 80 },
  { label: '姓名', prop: 'name', minWidth: 120 },
  { label: '邮箱', prop: 'email', minWidth: 180 },
  { 
    label: '状态', 
    prop: 'status', 
    width: 100,
    formatter: (row) => row.status === 1 ? '启用' : '禁用'
  },
  { label: '创建时间', prop: 'createTime', width: 150 },
  { label: '操作', prop: 'action', width: 150, slot: 'action' }
])

// 分页配置
const pagination = ref({
  currentPage: 1,
  pageSize: 10,
  total: 3
})

// 表格加载状态
const tableLoading = ref(false)

// 图表配置 - 确保图表能正确显示
const chartOptions = ref({
  title: {
    text: '示例图表',
    left: 'center'
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    }
  },
  legend: {
    data: ['销量'],
    bottom: 10
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '15%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子'],
    axisLabel: {
      rotate: 30
    }
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      name: '销量',
      type: 'bar',
      data: [5, 20, 36, 10, 10, 20],
      itemStyle: {
        color: '#409eff'
      },
      emphasis: {
        itemStyle: {
          color: '#67c23a'
        }
      }
    }
  ]
})

// 上传配置
const uploadAction = ref('/api/upload')
const fileList = ref([])

// 图片列表配置
const imgList = ref([
  { id: 1, url: 'https://picsum.photos/200/200?random=1', name: '图片1' },
  { id: 2, url: 'https://picsum.photos/200/200?random=2', name: '图片2' },
  { id: 3, url: 'https://picsum.photos/200/200?random=3', name: '图片3' }
])

// 富文本编辑器内容
const editorContent = ref('<p>这是一个富文本编辑器示例</p>')

// 选中的图标
const selectedIcon = ref('')

// 对话框状态
const showDialog = ref(false)

// 表单数据
const formData = reactive({
  name: '',
  email: ''
})

// 搜索事件
const handleSearch = (params) => {
  console.log('搜索参数:', params)
}

// 重置事件
const handleReset = () => {
  console.log('重置搜索')
}

// 表格查询搜索事件
const handleQuerySearch = (params) => {
  console.log('表格查询参数:', params)
}

// 表格查询重置事件
const handleQueryReset = () => {
  console.log('表格查询重置')
}

// 表格过滤器变化事件
const handleFilterChange = (filters) => {
  console.log('表格过滤器变化:', filters)
}

// 分页变化事件
const handlePaginationChange = (page) => {
  console.log('分页变化:', page)
}

// 行点击事件
const handleRowClick = (row) => {
  console.log('行点击:', row)
}

// 编辑事件
const handleEdit = (row) => {
  console.log('编辑:', row)
}

// 删除事件
const handleDelete = (row) => {
  console.log('删除:', row)
}

// 查看事件
const handleView = (row) => {
  console.log('查看:', row)
}

// 上传成功事件
const handleUploadSuccess = (response, file, fileList) => {
  console.log('上传成功:', response, file, fileList)
}

// 上传失败事件
const handleUploadError = (error, file, fileList) => {
  console.log('上传失败:', error, file, fileList)
}

// 文件移除事件
const handleFileRemove = (file, fileList) => {
  console.log('文件移除:', file, fileList)
}

// 图片删除事件
const handleImgDelete = (id) => {
  console.log('图片删除:', id)
}

// 富文本编辑器变化事件
const handleEditorChange = (content) => {
  console.log('富文本编辑器内容变化:', content)
}

// 图标选择变化事件
const handleIconChange = (icon) => {
  console.log('图标选择变化:', icon)
}

// 对话框确认事件
const handleDialogConfirm = () => {
  console.log('对话框确认:', formData)
  showDialog.value = false
}

// 对话框取消事件
const handleDialogCancel = () => {
  console.log('对话框取消')
  showDialog.value = false
}
</script>

<style scoped>
.comprehensive-example {
  padding: 20px;
  min-height: 100vh;
  background: #f5f7fa;
}

.page-header {
  margin-bottom: 30px;
  padding: 20px;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.page-header h1 {
  margin: 0 0 10px 0;
  font-size: 24px;
  font-weight: bold;
  color: #303133;
}

.page-header p {
  margin: 0;
  color: #606266;
  font-size: 14px;
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.section-group {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 20px;
}

.example-section {
  background: #fff;
  padding: 24px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s ease;
}

.example-section:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.example-section h2 {
  margin: 0 0 20px 0;
  font-size: 18px;
  font-weight: bold;
  color: #303133;
  border-bottom: 2px solid #409eff;
  padding-bottom: 10px;
}

.chart-section {
  grid-column: 1 / -1;
}

.chart-wrapper {
  width: 100%;
  height: 300px;
}

.selected-icon {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-top: 20px;
  padding: 12px;
  background: #f5f7fa;
  border-radius: 6px;
  border: 1px solid #e4e7ed;
}

.selected-icon span {
  font-size: 14px;
  color: #606266;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .comprehensive-example {
    padding: 10px;
  }
  
  .page-header {
    padding: 15px;
  }
  
  .section-group {
    grid-template-columns: 1fr;
  }
  
  .example-section {
    padding: 16px;
  }
  
  .example-section h2 {
    font-size: 16px;
  }
}
</style>
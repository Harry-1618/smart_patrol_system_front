<template>
  <div class="report">
    <el-card>
      <template #header>
        <div class="card-header">
          <span>报表管理</span>
          <div class="header-actions">
            <el-button type="primary" @click="generateReport">生成报表</el-button>
            <el-button type="success" @click="exportReport">导出报表</el-button>
          </div>
        </div>
      </template>

      <el-form :inline="true" :model="searchForm" class="search-form">
        <el-form-item label="报表类型">
          <el-select v-model="searchForm.type" placeholder="请选择">
            <el-option label="日报" value="daily" />
            <el-option label="周报" value="weekly" />
            <el-option label="月报" value="monthly" />
          </el-select>
        </el-form-item>
        <el-form-item label="日期范围">
          <el-date-picker
            v-model="searchForm.dateRange"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          />
        </el-form-item>
        <el-form-item>
          <el-button type="primary">查询</el-button>
          <el-button>重置</el-button>
        </el-form-item>
      </el-form>

      <el-tabs v-model="activeTab" class="report-tabs">
        <el-tab-pane label="巡更报表" name="patrol">
          <el-table :data="patrolReportData" style="width: 100%">
            <el-table-column prop="date" label="日期" width="120" />
            <el-table-column prop="staff" label="巡更人员" width="120" />
            <el-table-column prop="circles" label="巡更圈数" width="100" />
            <el-table-column prop="duration" label="巡更时长" width="120" />
            <el-table-column prop="status" label="状态" width="100">
              <template #default="scope">
                <el-tag :type="getStatusType(scope.row.status)">
                  {{ scope.row.status }}
                </el-tag>
              </template>
            </el-table-column>
            <el-table-column prop="route" label="巡更路线" />
            <el-table-column label="操作" width="150">
              <template #default="scope">
                <el-button size="small" @click="viewReport(scope.row)">查看</el-button>
                <el-button size="small" type="primary" @click="exportReportItem(scope.row)">导出</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>

        <el-tab-pane label="缺勤报表" name="absence">
          <el-table :data="absenceReportData" style="width: 100%">
            <el-table-column prop="date" label="日期" width="120" />
            <el-table-column prop="staff" label="缺勤人员" width="120" />
            <el-table-column prop="reason" label="缺勤原因" />
            <el-table-column prop="status" label="审批状态" width="100">
              <template #default="scope">
                <el-tag :type="getApprovalStatusType(scope.row.status)">
                  {{ scope.row.status }}
                </el-tag>
              </template>
            </el-table-column>
            <el-table-column label="操作" width="150">
              <template #default="scope">
                <el-button size="small" @click="viewAbsence(scope.row)">查看</el-button>
                <el-button size="small" type="primary" @click="approveAbsence(scope.row)">审批</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>

        <el-tab-pane label="设备故障报表" name="fault">
          <el-table :data="faultReportData" style="width: 100%">
            <el-table-column prop="date" label="日期" width="120" />
            <el-table-column prop="camera" label="摄像头" width="150" />
            <el-table-column prop="type" label="故障类型" width="120" />
            <el-table-column prop="description" label="故障描述" />
            <el-table-column prop="status" label="处理状态" width="100">
              <template #default="scope">
                <el-tag :type="getFaultStatusType(scope.row.status)">
                  {{ scope.row.status }}
                </el-tag>
              </template>
            </el-table-column>
            <el-table-column label="操作" width="150">
              <template #default="scope">
                <el-button size="small" @click="viewFault(scope.row)">查看</el-button>
                <el-button size="small" type="primary" @click="handleFault(scope.row)">处理</el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
      </el-tabs>

      <div class="pagination">
        <el-pagination
          v-model:current-page="currentPage"
          v-model:page-size="pageSize"
          :page-sizes="[10, 20, 50, 100]"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        />
      </div>
    </el-card>

    <!-- 生成报表对话框 -->
    <el-dialog v-model="reportDialogVisible" title="生成报表" width="50%">
      <el-form :model="reportForm" label-width="100px">
        <el-form-item label="报表类型">
          <el-select v-model="reportForm.type" placeholder="请选择">
            <el-option label="日报" value="daily" />
            <el-option label="周报" value="weekly" />
            <el-option label="月报" value="monthly" />
          </el-select>
        </el-form-item>
        <el-form-item label="日期范围">
          <el-date-picker
            v-model="reportForm.dateRange"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          />
        </el-form-item>
        <el-form-item label="报表内容">
          <el-checkbox-group v-model="reportForm.content">
            <el-checkbox label="patrol">巡更数据</el-checkbox>
            <el-checkbox label="absence">缺勤数据</el-checkbox>
            <el-checkbox label="fault">设备故障</el-checkbox>
          </el-checkbox-group>
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="reportDialogVisible = false">取消</el-button>
          <el-button type="primary" @click="submitReport">生成</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const activeTab = ref('patrol')
const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(100)
const reportDialogVisible = ref(false)

const searchForm = ref({
  type: '',
  dateRange: []
})

const reportForm = ref({
  type: '',
  dateRange: [],
  content: []
})

const patrolReportData = ref([
  {
    date: '2024-03-20',
    staff: '张三',
    circles: 4,
    duration: '4小时',
    status: '正常',
    route: 'A区 -> B区 -> C区 -> D区'
  },
  {
    date: '2024-03-20',
    staff: '李四',
    circles: 2,
    duration: '2小时',
    status: '异常',
    route: 'B区 -> C区'
  }
])

const absenceReportData = ref([
  {
    date: '2024-03-20',
    staff: '王五',
    reason: '病假',
    status: '待审批'
  }
])

const faultReportData = ref([
  {
    date: '2024-03-20',
    camera: 'A区1号摄像头',
    type: '画面丢失',
    description: '摄像头无信号',
    status: '处理中'
  }
])

const getStatusType = (status) => {
  const statusMap = {
    '正常': 'success',
    '异常': 'danger'
  }
  return statusMap[status]
}

const getApprovalStatusType = (status) => {
  const statusMap = {
    '待审批': 'warning',
    '已通过': 'success',
    '已拒绝': 'danger'
  }
  return statusMap[status]
}

const getFaultStatusType = (status) => {
  const statusMap = {
    '待处理': 'warning',
    '处理中': 'primary',
    '已解决': 'success'
  }
  return statusMap[status]
}

const generateReport = () => {
  reportDialogVisible.value = true
}

const exportReport = () => {
  // 实现导出报表功能
}

const viewReport = (row) => {
  // 实现查看报表功能
}

const exportReportItem = (row) => {
  // 实现导出单个报表功能
}

const viewAbsence = (row) => {
  // 实现查看缺勤详情功能
}

const approveAbsence = (row) => {
  // 实现审批缺勤功能
}

const viewFault = (row) => {
  // 实现查看故障详情功能
}

const handleFault = (row) => {
  // 实现处理故障功能
}

const submitReport = () => {
  // 实现生成报表功能
  reportDialogVisible.value = false
}
</script>

<style scoped>
.report {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header-actions {
  display: flex;
  gap: 10px;
}

.search-form {
  margin-bottom: 20px;
}

.report-tabs {
  margin-bottom: 20px;
}

.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}
</style> 
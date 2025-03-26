<template>
  <div class="patrol">
    <el-card>
      <template #header>
        <div class="card-header">
          <span>巡更管理</span>
          <el-button type="primary">导出报表</el-button>
        </div>
      </template>

      <el-form :inline="true" :model="searchForm" class="search-form">
        <el-form-item label="巡更人员">
          <el-select v-model="searchForm.patrolStaff" placeholder="请选择">
            <el-option label="全部" value="" />
            <el-option label="张三" value="zhangsan" />
            <el-option label="李四" value="lisi" />
            <el-option label="王五" value="wangwu" />
          </el-select>
        </el-form-item>
        <el-form-item label="巡更状态">
          <el-select v-model="searchForm.status" placeholder="请选择">
            <el-option label="全部" value="" />
            <el-option label="已完成" value="completed" />
            <el-option label="进行中" value="in_progress" />
            <el-option label="未开始" value="not_started" />
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

      <el-table :data="patrolData" style="width: 100%">
        <el-table-column prop="name" label="巡更人员" width="120" />
        <el-table-column prop="date" label="巡更日期" width="180" />
        <el-table-column prop="startTime" label="开始时间" width="120" />
        <el-table-column prop="endTime" label="结束时间" width="120" />
        <el-table-column prop="status" label="状态" width="100">
          <template #default="scope">
            <el-tag :type="getStatusType(scope.row.status)">
              {{ scope.row.status }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="circles" label="巡更圈数" width="100" />
        <el-table-column prop="route" label="巡更路线" />
        <el-table-column label="操作" width="200">
          <template #default="scope">
            <el-button size="small" @click="viewDetails(scope.row)">查看详情</el-button>
            <el-button size="small" type="primary" @click="viewTrack(scope.row)">查看轨迹</el-button>
            <el-button size="small" type="danger" @click="reportAbsence(scope.row)">缺勤上报</el-button>
          </template>
        </el-table-column>
      </el-table>

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

    <!-- 查看详情对话框 -->
    <el-dialog v-model="detailsVisible" title="巡更详情" width="50%">
      <el-descriptions :column="2" border>
        <el-descriptions-item label="巡更人员">张三</el-descriptions-item>
        <el-descriptions-item label="巡更日期">2024-03-20</el-descriptions-item>
        <el-descriptions-item label="开始时间">08:00:00</el-descriptions-item>
        <el-descriptions-item label="结束时间">12:00:00</el-descriptions-item>
        <el-descriptions-item label="巡更圈数">4圈</el-descriptions-item>
        <el-descriptions-item label="巡更状态">已完成</el-descriptions-item>
        <el-descriptions-item label="巡更路线" :span="2">
          A区 -> B区 -> C区 -> D区
        </el-descriptions-item>
        <el-descriptions-item label="备注" :span="2">
          正常完成巡更任务
        </el-descriptions-item>
      </el-descriptions>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const searchForm = ref({
  patrolStaff: '',
  status: '',
  dateRange: []
})

const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(100)
const detailsVisible = ref(false)

const patrolData = ref([
  {
    name: '张三',
    date: '2024-03-20',
    startTime: '08:00:00',
    endTime: '12:00:00',
    status: '已完成',
    circles: 4,
    route: 'A区 -> B区 -> C区 -> D区'
  },
  {
    name: '李四',
    date: '2024-03-20',
    startTime: '09:00:00',
    endTime: '11:00:00',
    status: '进行中',
    circles: 2,
    route: 'B区 -> C区 -> A区'
  },
  {
    name: '王五',
    date: '2024-03-20',
    startTime: '-',
    endTime: '-',
    status: '未开始',
    circles: 0,
    route: '-'
  }
])

const getStatusType = (status) => {
  const statusMap = {
    '已完成': 'success',
    '进行中': 'primary',
    '未开始': 'info'
  }
  return statusMap[status]
}

const viewDetails = (row) => {
  detailsVisible.value = true
}

const viewTrack = (row) => {
  // 实现查看轨迹功能
}

const reportAbsence = (row) => {
  // 实现缺勤上报功能
}
</script>

<style scoped>
.patrol {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.search-form {
  margin-bottom: 20px;
}

.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}
</style> 
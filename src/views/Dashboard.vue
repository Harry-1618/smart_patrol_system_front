<template>
  <div class="dashboard">
    <el-row :gutter="20">
      <el-col :span="6">
        <el-card class="box-card">
          <template #header>
            <div class="card-header">
              <span>今日巡更人数</span>
            </div>
          </template>
          <div class="card-content">
            <el-statistic :value="12">
              <template #prefix>
                <el-icon><User /></el-icon>
              </template>
            </el-statistic>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card class="box-card">
          <template #header>
            <div class="card-header">
              <span>摄像头状态</span>
            </div>
          </template>
          <div class="card-content">
            <el-statistic :value="45">
              <template #prefix>
                <el-icon><VideoCamera /></el-icon>
              </template>
              <template #suffix>/ 50</template>
            </el-statistic>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card class="box-card">
          <template #header>
            <div class="card-header">
              <span>今日缺勤</span>
            </div>
          </template>
          <div class="card-content">
            <el-statistic :value="2" value-style="color: #f56c6c">
              <template #prefix>
                <el-icon><Warning /></el-icon>
              </template>
            </el-statistic>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card class="box-card">
          <template #header>
            <div class="card-header">
              <span>待处理故障</span>
            </div>
          </template>
          <div class="card-content">
            <el-statistic :value="3" value-style="color: #e6a23c">
              <template #prefix>
                <el-icon><Tools /></el-icon>
              </template>
            </el-statistic>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <el-col :span="16">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>巡更轨迹实时监控</span>
            </div>
          </template>
          <div class="map-container">
            <!-- 这里放置地图组件 -->
            <div class="map-placeholder">地图区域</div>
          </div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>待处理事项</span>
            </div>
          </template>
          <el-timeline>
            <el-timeline-item
              v-for="(activity, index) in activities"
              :key="index"
              :timestamp="activity.time"
              :type="activity.type"
            >
              {{ activity.content }}
            </el-timeline-item>
          </el-timeline>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { User, VideoCamera, Warning, Tools } from '@element-plus/icons-vue'

const activities = ref([
  {
    content: '张三缺勤未完成巡更任务',
    time: '10:30',
    type: 'danger'
  },
  {
    content: 'A区3号摄像头故障',
    time: '09:15',
    type: 'warning'
  },
  {
    content: '李四完成巡更任务',
    time: '08:45',
    type: 'success'
  }
])
</script>

<style scoped>
.dashboard {
  padding: 20px;
}

.mt-20 {
  margin-top: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-content {
  text-align: center;
  padding: 20px 0;
}

.map-container {
  height: 400px;
}

.map-placeholder {
  height: 100%;
  background-color: #f5f7fa;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #909399;
  font-size: 16px;
}
</style> 
<template>
  <div class="camera">
    <el-card>
      <template #header>
        <div class="card-header">
          <span>摄像头管理</span>
          <el-button type="primary">添加摄像头</el-button>
        </div>
      </template>

      <el-row :gutter="20">
        <el-col :span="6" v-for="camera in cameras" :key="camera.id">
          <el-card class="camera-card" :body-style="{ padding: '0px' }">
            <div class="camera-status" :class="camera.status">
              <el-tag :type="getStatusType(camera.status)">
                {{ camera.status }}
              </el-tag>
            </div>
            <div class="camera-preview">
              <img :src="camera.preview" class="preview-image">
            </div>
            <div class="camera-info">
              <h3>{{ camera.name }}</h3>
              <p>位置：{{ camera.location }}</p>
              <p>IP地址：{{ camera.ip }}</p>
              <p>最后更新时间：{{ camera.lastUpdate }}</p>
            </div>
            <div class="camera-actions">
              <el-button size="small" @click="viewCamera(camera)">查看</el-button>
              <el-button size="small" type="primary" @click="editCamera(camera)">编辑</el-button>
              <el-button size="small" type="danger" @click="reportFault(camera)">故障上报</el-button>
            </div>
          </el-card>
        </el-col>
      </el-row>

      <div class="pagination">
        <el-pagination
          v-model:current-page="currentPage"
          v-model:page-size="pageSize"
          :page-sizes="[8, 16, 24, 32]"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        />
      </div>
    </el-card>

    <!-- 故障上报对话框 -->
    <el-dialog v-model="faultDialogVisible" title="故障上报" width="50%">
      <el-form :model="faultForm" label-width="100px">
        <el-form-item label="摄像头">
          <el-input v-model="faultForm.cameraName" disabled />
        </el-form-item>
        <el-form-item label="故障类型">
          <el-select v-model="faultForm.type" placeholder="请选择故障类型">
            <el-option label="画面丢失" value="no_signal" />
            <el-option label="画面模糊" value="blurry" />
            <el-option label="设备离线" value="offline" />
            <el-option label="其他" value="other" />
          </el-select>
        </el-form-item>
        <el-form-item label="故障描述">
          <el-input
            v-model="faultForm.description"
            type="textarea"
            :rows="4"
            placeholder="请详细描述故障情况"
          />
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="faultDialogVisible = false">取消</el-button>
          <el-button type="primary" @click="submitFault">提交</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const currentPage = ref(1)
const pageSize = ref(8)
const total = ref(32)
const faultDialogVisible = ref(false)
const faultForm = ref({
  cameraName: '',
  type: '',
  description: ''
})

const cameras = ref([
  {
    id: 1,
    name: 'A区1号摄像头',
    location: 'A区大门',
    ip: '192.168.1.101',
    status: '正常',
    lastUpdate: '2024-03-20 10:30:00',
    preview: 'https://placeholder.com/300x200'
  },
  {
    id: 2,
    name: 'A区2号摄像头',
    location: 'A区停车场',
    ip: '192.168.1.102',
    status: '故障',
    lastUpdate: '2024-03-20 09:15:00',
    preview: 'https://placeholder.com/300x200'
  },
  {
    id: 3,
    name: 'B区1号摄像头',
    location: 'B区大门',
    ip: '192.168.1.201',
    status: '正常',
    lastUpdate: '2024-03-20 10:35:00',
    preview: 'https://placeholder.com/300x200'
  },
  {
    id: 4,
    name: 'B区2号摄像头',
    location: 'B区停车场',
    ip: '192.168.1.202',
    status: '维护中',
    lastUpdate: '2024-03-20 10:00:00',
    preview: 'https://placeholder.com/300x200'
  }
])

const getStatusType = (status) => {
  const statusMap = {
    '正常': 'success',
    '故障': 'danger',
    '维护中': 'warning'
  }
  return statusMap[status]
}

const viewCamera = (camera) => {
  // 实现查看摄像头功能
}

const editCamera = (camera) => {
  // 实现编辑摄像头功能
}

const reportFault = (camera) => {
  faultForm.value.cameraName = camera.name
  faultDialogVisible.value = true
}

const submitFault = () => {
  // 实现故障上报功能
  faultDialogVisible.value = false
}
</script>

<style scoped>
.camera {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.camera-card {
  margin-bottom: 20px;
  position: relative;
}

.camera-status {
  position: absolute;
  top: 10px;
  right: 10px;
  z-index: 1;
}

.camera-preview {
  height: 200px;
  overflow: hidden;
}

.preview-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.camera-info {
  padding: 14px;
}

.camera-info h3 {
  margin: 0 0 10px 0;
}

.camera-info p {
  margin: 5px 0;
  color: #666;
  font-size: 14px;
}

.camera-actions {
  padding: 14px;
  border-top: 1px solid #eee;
  display: flex;
  justify-content: space-between;
}

.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}
</style> 
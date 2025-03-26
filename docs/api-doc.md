# 智能巡更系统接口文档

## 基础信息

- 基础路径：`/api/v1`
- 请求方式：REST API
- 数据格式：JSON
- 认证方式：Bearer Token

## 1. 用户认证

### 1.1 登录

- 请求路径：`/auth/login`
- 请求方式：POST
- 请求参数：

```json
{
  "username": "string",  // 用户名
  "password": "string"   // 密码
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "token": "string",      // JWT token
    "user": {
      "id": "string",
      "username": "string", // 用户名
      "name": "string",     // 姓名
      "role": "string",     // 角色：admin/manager/patrol
      "phone": "string",    // 手机号
      "email": "string"     // 邮箱
    }
  },
  "message": "登录成功"
}
```

### 1.2 退出登录

- 请求路径：`/auth/logout`
- 请求方式：POST
- 请求头：`Authorization: Bearer <token>`
- 响应数据：

```json
{
  "code": 200,
  "message": "退出成功"
}
```

## 2. 巡更管理

### 2.1 获取巡更记录列表

- 请求路径：`/patrol/records`
- 请求方式：GET
- 请求参数：

```json
{
  "patrolStaff": "string",  // 巡更人员ID（可选）
  "status": "string",       // 状态：completed/in_progress/not_started（可选）
  "startDate": "string",    // 开始日期，格式：YYYY-MM-DD（可选）
  "endDate": "string",      // 结束日期，格式：YYYY-MM-DD（可选）
  "page": "number",         // 页码，默认1
  "pageSize": "number"      // 每页条数，默认10
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "total": "number",
    "records": [{
      "id": "string",
      "staffId": "string",
      "staffName": "string",
      "date": "string",
      "startTime": "string",
      "endTime": "string",
      "status": "string",
      "circles": "number",
      "route": "string"
    }]
  },
  "message": "获取成功"
}
```

### 2.2 获取巡更轨迹

- 请求路径：`/patrol/tracks/:recordId`
- 请求方式：GET
- 响应数据：

```json
{
  "code": 200,
  "data": {
    "recordId": "string",
    "tracks": [{
      "timestamp": "string",
      "location": {
        "x": "number",
        "y": "number",
        "area": "string"
      }
    }]
  },
  "message": "获取成功"
}
```

### 2.3 上报缺勤

- 请求路径：`/patrol/absence`
- 请求方式：POST
- 请求参数：

```json
{
  "staffId": "string",     // 巡更人员ID
  "date": "string",        // 日期，格式：YYYY-MM-DD
  "reason": "string",      // 缺勤原因
  "description": "string"  // 详细说明（可选）
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "id": "string",  // 缺勤记录ID
    "status": "待审批"
  },
  "message": "上报成功"
}
```

## 3. 摄像头管理

### 3.1 获取摄像头列表

- 请求路径：`/cameras`
- 请求方式：GET
- 请求参数：

```json
{
  "status": "string",  // 状态：normal/fault/maintenance（可选）
  "area": "string",    // 区域（可选）
  "page": "number",    // 页码，默认1
  "pageSize": "number" // 每页条数，默认10
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "total": "number",
    "cameras": [{
      "id": "string",
      "name": "string",
      "location": "string",
      "ip": "string",
      "status": "string",
      "lastUpdate": "string"
    }]
  },
  "message": "获取成功"
}
```

### 3.2 上报摄像头故障

- 请求路径：`/cameras/fault`
- 请求方式：POST
- 请求参数：

```json
{
  "cameraId": "string",    // 摄像头ID
  "type": "string",        // 故障类型：no_signal/blurry/offline/other
  "description": "string"  // 故障描述
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "id": "string",  // 故障记录ID
    "status": "待处理"
  },
  "message": "上报成功"
}
```

## 4. 报表管理

### 4.1 生成报表

- 请求路径：`/reports/generate`
- 请求方式：POST
- 请求参数：

```json
{
  "type": "string",        // 报表类型：daily/weekly/monthly
  "startDate": "string",   // 开始日期，格式：YYYY-MM-DD
  "endDate": "string",     // 结束日期，格式：YYYY-MM-DD
  "content": ["string"]    // 报表内容：patrol/absence/fault
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "reportId": "string",
    "url": "string"  // 报表下载链接
  },
  "message": "生成成功"
}
```

### 4.2 获取报表列表

- 请求路径：`/reports`
- 请求方式：GET
- 请求参数：

```json
{
  "type": "string",     // 报表类型：daily/weekly/monthly（可选）
  "startDate": "string",// 开始日期，格式：YYYY-MM-DD（可选）
  "endDate": "string",  // 结束日期，格式：YYYY-MM-DD（可选）
  "page": "number",     // 页码，默认1
  "pageSize": "number"  // 每页条数，默认10
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "total": "number",
    "reports": [{
      "id": "string",
      "type": "string",
      "startDate": "string",
      "endDate": "string",
      "content": ["string"],
      "createTime": "string",
      "url": "string"
    }]
  },
  "message": "获取成功"
}
```

## 5. 用户管理

### 5.1 创建用户

- 请求路径：`/users`
- 请求方式：POST
- 请求参数：

```json
{
  "username": "string",  // 用户名
  "password": "string",  // 密码
  "name": "string",      // 姓名
  "role": "string",      // 角色：admin/manager/patrol
  "phone": "string",     // 手机号
  "email": "string"      // 邮箱
}
```

- 响应数据：

```json
{
  "code": 200,
  "data": {
    "id": "string",
    "username": "string",
    "name": "string",
    "role": "string",
    "phone": "string",
    "email": "string",
    "createTime": "string"
  },
  "message": "创建成功"
}
```

### 5.2 重置密码

- 请求路径：`/users/:userId/reset-password`
- 请求方式：POST
- 响应数据：

```json
{
  "code": 200,
  "data": {
    "newPassword": "string"  // 新密码
  },
  "message": "重置成功"
}
```

## 错误码说明

- 200：成功
- 400：请求参数错误
- 401：未授权
- 403：权限不足
- 404：资源不存在
- 500：服务器内部错误

## 注意事项

1. 所有请求需要在 Header 中携带 Token：
```
Authorization: Bearer <token>
```

2. 时间格式统一使用 ISO 8601 标准：
- 日期：YYYY-MM-DD
- 时间：HH:mm:ss
- 日期时间：YYYY-MM-DD HH:mm:ss

3. 分页参数：
- page：从1开始
- pageSize：默认10，最大100

4. 文件上传和下载：
- 上传文件使用 multipart/form-data
- 下载文件通过返回的 url 地址进行下载 
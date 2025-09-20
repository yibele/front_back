# 前后端连接测试指南

## 🎯 完成的工作总结

### ✅ 已完成的连接工作

1. **前端更新**：
   - 修改了 `background.js` 连接到本地后端 `http://localhost:8080`
   - 更新了登录逻辑，支持手机号登录并调用后端API
   - 修改了单个文案提取功能，直接调用后端API
   - 添加了用户积分刷新功能
   - 更新了popup界面，支持手机号输入

2. **后端更新**：
   - 添加了CORS中间件支持浏览器插件跨域访问
   - 修改了视频文案提取API，集成Coze API调用
   - 保持了完整的用户认证和积分系统

## 🚀 测试步骤

### 第一步：启动后端服务器
```bash
cd /Users/yibeel/go/聚抖自媒体大师/go_back_end
go run cmd/server/main.go
```
服务器将在 `http://localhost:8080` 启动

### 第二步：创建测试用户
使用以下API创建测试用户：

```bash
curl -X POST http://localhost:8080/api/v1/register \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "13800138000",
    "password": "123456"
  }'
```

### 第三步：测试登录
```bash
curl -X POST http://localhost:8080/api/v1/login \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "13800138000",
    "password": "123456"
  }'
```

### 第四步：加载浏览器插件
1. 打开Chrome浏览器
2. 访问 `chrome://extensions/`
3. 启用"开发者模式"
4. 点击"加载已解压的扩展程序"
5. 选择 `/Users/yibeel/go/聚抖自媒体大师/front` 目录

### 第五步：测试插件登录
1. 点击插件图标打开popup
2. 输入手机号：13800138000
3. 输入密码：123456
4. 点击登录按钮

### 第六步：测试文案提取功能
1. 访问抖音用户主页（如：https://www.douyin.com/user/MS4wLjABAAAA...）
2. 等待页面加载，插件会自动拦截视频数据
3. 点击插件图标，点击"打开侧边栏"
4. 在侧边栏中点击具体视频，然后点击"提取文案"按钮

## 🔧 需要的预置数据

为了测试功能，需要在数据库中添加一些产品数据：

```sql
-- 插入视频文案提取产品
INSERT INTO products (name, description, price_cents, required_credits, status, created_at, updated_at) 
VALUES ('douyin_video_caption', '抖音视频文案提取', 0, 5, 'active', NOW(), NOW());

-- 插入主页分析产品  
INSERT INTO products (name, description, price_cents, required_credits, status, created_at, updated_at)
VALUES ('douyin_account_benchmark', '抖音主页分析', 0, 10, 'active', NOW(), NOW());

-- 插入批量视频文案提取产品
INSERT INTO products (name, description, price_cents, required_credits, status, created_at, updated_at)
VALUES ('douyin_home_videos_captions', '批量视频文案提取', 0, 20, 'active', NOW(), NOW());
```

## 📋 API端点映射

| 前端功能 | 后端API端点 | 说明 |
|---------|------------|------|
| 用户登录 | POST /api/v1/login | 手机号+密码登录 |
| 获取用户信息 | GET /api/v1/me | 获取用户详情和积分 |
| 单个文案提取 | POST /api/v1/douyin/video/caption | 提取单个视频文案 |
| 批量文案提取 | POST /api/v1/douyin/home/videos-with-captions | 批量提取文案 |
| 主页分析 | POST /api/v1/douyin/account/benchmark | 分析用户主页 |

## 🐛 可能的问题和解决方案

1. **CORS问题**：已添加CORS中间件，应该可以正常跨域访问
2. **JWT认证**：前端会自动在请求头中添加Bearer token
3. **积分不足**：新用户注册会获得注册奖励积分
4. **产品不存在**：需要手动插入产品数据到数据库

## 🎉 测试成功标志

- ✅ 用户可以在插件中成功登录
- ✅ 登录后可以看到用户积分信息
- ✅ 在抖音页面可以成功提取视频文案
- ✅ 提取后积分会相应减少
- ✅ 可以进行主页分析并获得分析报告

这样就完成了前后端用户系统的完整连接！

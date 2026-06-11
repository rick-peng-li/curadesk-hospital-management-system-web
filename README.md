<!-- Git 仓库地址：git@github.com:rick-peng-li/curadesk-hospital-management-system-web.git -->

# CuraDesk Hospital Management System Web

## 项目简介

CuraDesk 是一个面向医院与医疗服务场景的全栈 Web 项目，覆盖患者端、医生端、后台管理端以及 AI 辅助服务。

项目以预约挂号与服务预约为主线，整合了医生管理、服务管理、支付确认、消息通知、报告上传、OCR 文档解析、RAG 问答、症状分诊建议等能力，适合用于医疗信息化、MERN 全栈开发与 AI 集成场景演示。

## 核心能力

- 患者端支持医生浏览、服务浏览、预约下单、支付回调、历史预约查看
- 医生端支持登录、查看个人工作台、查看预约列表、编辑个人资料
- 管理端支持医生管理、服务管理、医生预约管理、服务预约管理、统计概览、主题切换
- 后端提供医生、服务、预约、报告、AI 问答、订阅等接口
- AI 服务支持症状分析、报告摘要、Embedding 生成、基于文档上下文的问答
- 文档处理链路支持 PDF 解析、OCR、文本清洗、分块、向量存储、检索增强回答

## 项目架构

### 整体分层

- `frontend`：患者端与医生端前台，基于 React + Vite
- `admin`：独立后台管理系统，基于 React + Vite
- `backend`：Node.js + Express API，负责业务接口、鉴权、支付、文件上传、数据库访问
- `ai-service`：FastAPI 微服务，负责 Gemini 推理、症状分析、摘要、向量生成
- `docker-compose.yml`：本地启动 Redis 与 ChromaDB 容器

### 业务流转

1. 患者在前台浏览医生或服务
2. 发起预约或服务预约请求
3. 后端创建订单并拉起 Stripe 支付流程
4. 支付回调后更新预约状态
5. 管理端和医生端可查看对应预约数据
6. 用户上传医疗报告后，后端完成 PDF/OCR/清洗/分块/Embedding/向量入库
7. 用户通过 RAG 接口继续围绕报告内容提问

## 技术栈

### 前端

- React 18 / React 19
- Vite 7
- React Router
- Clerk 鉴权
- Axios
- React Toastify
- Tailwind CSS
- Context API（管理端主题切换）

### 后端

- Node.js
- Express 5
- Mongoose
- Clerk Express Middleware
- Multer
- Stripe
- Cloudinary
- Nodemailer
- JWT（医生端登录）

### AI 与数据处理

- FastAPI
- Google Gemini / Google Generative AI
- LangChain
- LangGraph
- ChromaDB
- Redis
- Tesseract OCR
- pdf2json
- pdf-poppler

### 基础设施

- Docker Compose
- Redis 容器
- ChromaDB 容器

## 目录结构

```text
curadesk-hospital-management-system-web/
├─ admin/                     # 管理端
│  ├─ src/components/         # 后台页面组件
│  ├─ src/pages/              # 页面入口
│  └─ src/context/            # 主题上下文
├─ frontend/                  # 患者端 + 医生端
│  ├─ src/components/         # 通用页面组件与聊天组件
│  ├─ src/doctor/             # 医生端页面组件
│  └─ src/pages/              # 路由页面
├─ backend/                   # Node.js API
│  ├─ config/                 # MongoDB / Redis 配置
│  ├─ controllers/            # 业务控制器
│  ├─ middlewares/            # 医生鉴权、上传中间件
│  ├─ models/                 # MongoDB 模型
│  ├─ routes/                 # 路由定义
│  ├─ services/               # AI / RAG / OCR / 邮件等服务
│  └─ utilities/              # 工具函数
├─ ai-service/                # FastAPI AI 微服务
└─ docker-compose.yml         # Redis + ChromaDB 本地容器
```

## 模块与页面设计

### 患者端与医生端页面

| 路由 | 页面文件 | 功能说明 |
| --- | --- | --- |
| `/` | `frontend/src/pages/Home.jsx` | 首页，展示医院入口、推荐内容、聊天组件 |
| `/doctors` | `frontend/src/pages/Doctors.jsx` | 医生列表页，支持浏览医生与咨询入口 |
| `/doctors/:id` | `frontend/src/pages/DoctorDetail.jsx` | 医生详情页，展示医生信息并发起预约 |
| `/services` | `frontend/src/pages/Service.jsx` | 医疗服务列表页 |
| `/services/:id` | `frontend/src/pages/ServiceDetailPage.jsx` | 服务详情页，填写服务预约信息并进入支付 |
| `/contact` | `frontend/src/pages/Contact.jsx` | 联系页，承载联系信息与聊天组件 |
| `/appointments` | `frontend/src/pages/Appointments.jsx` | 当前用户预约记录页 |
| `/appointment/success` | `frontend/src/pages/VerifyPayPageStripe.jsx` | 医生预约支付成功回调页 |
| `/appointment/cancel` | `frontend/src/pages/VerifyPayPageStripe.jsx` | 医生预约支付取消回调页 |
| `/service-appointment/success` | `frontend/src/pages/VerifyServicePayPageStripe.jsx` | 服务预约支付成功回调页 |
| `/service-appointment/cancel` | `frontend/src/pages/VerifyServicePayPageStripe.jsx` | 服务预约支付取消回调页 |
| `/doctor-admin/login` | `frontend/src/pages/Login.jsx` | 医生后台登录页 |
| `/doctor-admin/:id` | `frontend/src/pages/DoctorHome.jsx` | 医生工作台首页 |
| `/doctor-admin/:id/appointments` | `frontend/src/pages/List.jsx` | 医生名下预约列表 |
| `/doctor-admin/:id/profile/edit` | `frontend/src/pages/EditProfile.jsx` | 医生资料编辑页 |

### 管理端页面

| 路由 | 页面文件 | 功能说明 |
| --- | --- | --- |
| `/` | `admin/src/components/Hero.jsx` | 管理端门户页 |
| `/h` | `admin/src/pages/Home.jsx` | 管理总览页，查看统计与核心入口 |
| `/add` | `admin/src/pages/Add.jsx` | 新增医生 |
| `/list` | `admin/src/pages/List.jsx` | 医生列表与维护 |
| `/appointments` | `admin/src/pages/Appointments.jsx` | 医生预约管理 |
| `/service-dashboard` | `admin/src/pages/SerDashboard.jsx` | 服务侧统计工作台 |
| `/add-service` | `admin/src/pages/AddSer.jsx` | 新增服务 |
| `/list-service` | `admin/src/pages/ListService.jsx` | 服务列表与维护 |
| `/service-appointments` | `admin/src/pages/ServiceAppointments.jsx` | 服务预约管理 |

## 后端接口设计

后端主入口位于 `backend/server.js`，当前统一挂载在 `http://localhost:4000`。

### 医生模块

接口前缀：`/api/doctors`

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| GET | `/api/doctors` | 获取医生列表 |
| GET | `/api/doctors/:id` | 获取医生详情 |
| POST | `/api/doctors/login` | 医生登录，返回医生侧身份凭证 |
| POST | `/api/doctors` | 新增医生，支持图片上传 |
| PUT | `/api/doctors/:id` | 更新医生信息 |
| POST | `/api/doctors/:id/toggle-availability` | 切换医生可预约状态 |
| DELETE | `/api/doctors/:id` | 删除医生 |

### 服务模块

接口前缀：`/api/services`

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| GET | `/api/services` | 获取服务列表 |
| GET | `/api/services/:id` | 获取服务详情 |
| POST | `/api/services` | 新增服务，支持图片上传 |
| PUT | `/api/services/:id` | 更新服务信息 |
| DELETE | `/api/services/:id` | 删除服务 |

### 医生预约模块

接口前缀：`/api/appointments`

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| GET | `/api/appointments` | 管理端查看所有医生预约 |
| GET | `/api/appointments/confirm` | Stripe 支付确认回调 |
| GET | `/api/appointments/stats/summary` | 获取预约汇总统计 |
| POST | `/api/appointments` | 创建医生预约，需要 Clerk 登录 |
| GET | `/api/appointments/me` | 获取当前登录用户预约 |
| GET | `/api/appointments/doctor/:doctorId` | 获取指定医生的预约列表 |
| POST | `/api/appointments/:id/cancel` | 取消预约 |
| GET | `/api/appointments/patients/count` | 统计注册患者数量 |
| PUT | `/api/appointments/:id` | 更新预约状态或时间 |

### 服务预约模块

接口前缀：`/api/service-appointments`

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| GET | `/api/service-appointments` | 获取服务预约列表 |
| POST | `/api/service-appointments` | 创建服务预约 |
| GET | `/api/service-appointments/confirm` | 服务预约支付确认回调 |
| GET | `/api/service-appointments/stats/summary` | 服务预约统计 |
| GET | `/api/service-appointments/:id` | 获取服务预约详情 |
| PUT | `/api/service-appointments/:id` | 更新服务预约状态 |
| POST | `/api/service-appointments/:id/cancel` | 取消服务预约 |

### AI 与报告模块

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| POST | `/api/chatbot` | 症状问答与医生推荐入口 |
| POST | `/api/reports/upload` | 上传医疗报告，执行 PDF 解析、OCR、摘要、分块与向量化 |
| POST | `/api/rag/ask` | 基于报告上下文进行问答 |
| GET | `/api/rag/history` | 获取报告问答历史 |
| GET | `/api/test` | AI 症状分析调试接口 |

### 订阅模块

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| POST | `/api/newsletter/subscribe` | 邮件订阅 |

## AI 微服务接口设计

AI 微服务默认建议运行在 `http://localhost:8001`。

| 方法 | 路径 | 功能 |
| --- | --- | --- |
| GET | `/` | 服务运行状态 |
| GET | `/health` | 健康检查 |
| POST | `/summarize` | 生成报告摘要 |
| POST | `/symptom-analysis` | 症状分诊分析 |
| POST | `/rag-answer` | 根据传入上下文回答问题 |
| POST | `/embed-report` | 生成文本向量 |

## 数据与模型说明

### 主要数据实体

- `doctor`：医生基础资料、可预约状态、头像、专业信息
- `Service`：医院服务项目、价格、时长、描述、图片
- `Appointment`：患者与医生之间的预约记录、状态、支付信息
- `serviceAppointment`：患者与服务项目之间的预约记录
- `Newsletter`：订阅邮箱数据
- `reportModel`：上传报告与摘要、向量化相关信息
- `chatSessionModel` / `messageModel`：RAG 对话会话与消息历史

### 鉴权方式

- 患者端：Clerk 登录态，接口通过 Clerk Middleware 校验
- 管理端：当前前端通过 Clerk 登录控制页面访问
- 医生端：通过 JWT 中间件 `doctorAuth` 保护医生资料更新与状态切换接口

## 启动方式

### 1. 克隆仓库

```bash
git clone git@github.com:rick-peng-li/curadesk-hospital-management-system-web.git
cd curadesk-hospital-management-system-web
```

### 2. 启动基础设施

确保本机已安装并启动 Docker Desktop。

```bash
docker-compose up -d
```

默认会启动：

- ChromaDB：`http://localhost:8000`
- Redis：`redis://localhost:6379`

### 3. 启动后端

```bash
cd backend
npm install
npm start
```

默认端口：`http://localhost:4000`

### 4. 启动患者端与医生端前台

```bash
cd frontend
npm install
npm run dev
```

默认端口通常为：`http://localhost:5173`

### 5. 启动管理端

```bash
cd admin
npm install
npm run dev
```

默认端口通常为：`http://localhost:5174`

### 6. 启动 AI 微服务

当前仓库未提供 `requirements.txt`，可手动安装最小依赖：

```bash
cd ai-service
pip install fastapi uvicorn google-generativeai python-dotenv
uvicorn main:app --host 0.0.0.0 --port 8001 --reload
```

## 环境变量

### backend/.env

```env
MONGO_URI=
CLERK_SECRET_KEY=
STRIPE_SECRET_KEY=
FRONTEND_URL=http://localhost:5173
JWT_SECRET=
MAJOR_ADMIN_ID=
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
EMAIL_USER=
EMAIL_PASS=
GEMINI_API_KEY=
```

### frontend/.env

```env
VITE_CLERK_PUBLISHABLE_KEY=
VITE_API_URL=http://localhost:4000
```

### admin/.env

```env
VITE_CLERK_PUBLISHABLE_KEY=
```

### ai-service/.env

```env
GEMINI_API_KEY=
```

## 接口与页面联动说明

### 患者预约链路

- 前台详情页提交预约数据到后端
- 后端创建 Stripe Checkout Session
- 前台跳转支付
- 支付成功后回调到成功页
- 成功页再向后端确认预约结果并刷新预约状态

### 服务预约链路

- 服务详情页提交服务预约信息
- 后端创建服务类支付订单
- 支付回调完成服务预约状态更新
- 管理端在服务预约页查看与处理订单

### 医疗报告 AI 链路

- 用户上传 PDF 报告到 `/api/reports/upload`
- 后端提取 PDF 文本，必要时补充 OCR
- 文本清洗、分块并生成 Embedding
- 向量存入 ChromaDB
- 用户通过 `/api/rag/ask` 基于报告内容继续追问
- Redis 与消息模型保存对话上下文

## 开发建议

- 将后端、AI 微服务、前端端口统一写入环境变量，减少硬编码地址
- 为 `ai-service` 补充 `requirements.txt`，便于一键安装
- 为后端补充接口文档或 OpenAPI 描述，方便联调
- 将上传与临时文件统一放到忽略目录，避免无关二进制进入仓库
- 后续可增加测试脚本与 CI 校验，提升交付稳定性

## 当前仓库整理说明

本次整理重点做了以下收敛：

- 重写 README，改为中文说明并补齐模块、页面、接口、架构、启动方式
- 更新根目录 `.gitignore`，补充 Python 缓存、临时文件、日志、构建产物等忽略规则
- 移除未被引用的页面、调试资源、缓存文件和临时文件，减少仓库噪音
- 去掉 README 对大体积截图资源的依赖，降低仓库冗余内容占比

# classroom-biometric-system# 基于生物识别的课堂管理系统

一个功能完整的Java课堂管理系统，集成了人脸识别和头部姿态检测技术，实时监测学生课堂专注度。

## 🎯 核心功能

### 1. 生物识别模块
- **人脸检测** - 使用OpenCV级联分类器检测学生人脸
- **头部姿态识别** - 识别4种姿态：直视、低头、抬头、歪头
- **实时处理** - 支持视频流实时处理

### 2. 专注度分析
- **动态评分** - 根据头部姿态和置信度计算专注度分数（0-100）
- **趋势分析** - 跟踪课堂期间的专注度变化
- **等级评定** - 优秀、良好、一般、需改进四个等级

### 3. 课堂管理
- **课程信息** - 管理课程名称、时间、地点
- **学生管理** - 学号、姓名、班级、联系方式等
- **生物特征存储** - 安全存储学生人脸特征向量

### 4. 数据统计
- **详细记录** - 每帧检测结果的完整记录
- **汇总报告** - 课堂级别的统计分析
- **个人报表** - 学生个人的专注度报告

## 🛠 技术栈

| 层次 | 技术 | 版本 |
|------|------|------|
| 后端框架 | Spring Boot | 2.7.14 |
| ORM | Spring Data JPA | - |
| 数���库 | MySQL | 8.0+ |
| 缓存 | Redis | 6.0+ |
| 人脸识别 | OpenCV | 4.8.0 |
| 构建工具 | Maven | 3.6+ |
| Java版本 | JDK | 1.8+ |

## 📁 项目结构

```
classroom-biometric-system/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/classroom/
│   │   │       ├── entity/              # 数据实体
│   │   │       ├── repository/          # 数据访问层
│   │   │       ├── service/
│   │   │       │   ├── biometric/      # 生物识别服务
│   │   │       │   └── AttentionService # 专注度服务
│   │   │       └── controller/          # REST接口
│   │   └── resources/
│   │       ├── application.yml
│   │       └── logback-spring.xml
│   └── test/
├── sql/                                 # 数据库初始化脚本
├── pom.xml
└── README. md
```

## 🚀 快速开始

### 前置条件
- JDK 1.8+
- Maven 3.6+
- MySQL 8.0+
- Redis 6.0+

### 安装步骤

#### 1. 克隆项目
```bash
git clone https://github.com/springbootAop/classroom-biometric-system. git
cd classroom-biometric-system
```

#### 2. 配置数据库
```bash
# 创建数据库并初始化表
mysql -u root -p < sql/init.sql
```

#### 3. 修改配置文件
编辑 `src/main/resources/application.yml`，更新：
- 数据库连接信息
- Redis连接信息
- OpenCV级联分类器路径

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/classroom_db
    username: root
    password:  your_password
  redis:
    host: localhost
    port: 6379
```

#### 4. 下载OpenCV级联分类器
```bash
# 人脸检测所需的级联分类器
# 下载位置：https://github.com/opencv/opencv/tree/master/data/haarcascades
# 放置在系统路径或修改配置文件中的路径
```

#### 5. 编译并运行
```bash
# 编译项目
mvn clean install

# 运行应用
mvn spring-boot: run

# 或打包为JAR运行
mvn clean package
java -jar target/classroom-biometric-system-1.0.0.jar
```

应用启动后访问：`http://localhost:8080`

## 📊 API文档

### 专注度相关接口

#### 获取学生课堂专注度数据
```http
GET /api/attention/{classroomId}/{studentId}

Response:
{
    "code": 200,
    "message": "Success",
    "data": {
        "id": 1,
        "classroomId": 1,
        "studentId": 1,
        "averageScore": 85.5,
        "lowHeadCount":  5,
        "highHeadCount": 2,
        "tiltHeadCount": 1,
        "totalDetections":  100
    }
}
```

#### 获取课堂所有学生专注度数据
```http
GET /api/attention/classroom/{classroomId}

Response:
{
    "code": 200,
    "message": "Success",
    "data": [... ],
    "total": 30
}
```

#### 生成课堂总结
```http
POST /api/attention/summary/{classroomId}

Response: 
{
    "code": 200,
    "message": "Summary generated successfully"
}
```

### 学生管理接口

#### 创建学生
```http
POST /api/students
Content-Type: application/json

{
    "studentId": "STU001",
    "name":  "张三",
    "classId": 1,
    "grade": "高一",
    "email": "student@example.com",
    "phone": "13800000000"
}
```

#### 获取学生信息
```http
GET /api/students/{id}
```

#### 获取班级所有学生
```http
GET /api/students/class/{classId}
```

#### 更新学生信息
```http
PUT /api/students/{id}
```

## 🔄 工作流程

### 课堂监测流程

```
┌─────────────────┐
│  开始课堂       │
│  (启动监测)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  实时视频捕获   │
│  (每帧处理)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  人脸检测       │
│  (OpenCV)       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  头部姿态识别   │
│  (4种状态)      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  专注度评分     │
│  (0-100)        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  数据存储       │
│  (数据库)       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  课堂结束       │
│  (生成报告)     │
└─────────────────┘
```

### 专注度计算规则

| 头部姿态 | 基础分数 | 说明 |
|---------|--------|------|
| 直视 (0) | 100 | 正常专注 |
| 低头 (1) | 70 | 可能在记笔记 |
| 抬头 (2) | 40 | 分散注意力 |
| 歪头 (3) | 30 | 分散注意力 |

最终分数 = 基础分数 × (置信度/100)

## 🔒 安全特性

- ✅ 数据加密存储（生物特征数据）
- ✅ 访问控制和权限管理
- ✅ API请求验证
- ✅ 日志审计
- ✅ CORS跨域配置

## 📈 性能指标

- 支持实时处理 1080p 视频
- 单帧处理延迟 < 50ms
- 支持同时监测 50+ 学生
- 数据库查询优化

## 🐛 常见问题

### Q: 如何处理检测失败？
A: 系统会自动记录失败，继续处理下一帧。可在日志中查看详细错误信息。

### Q:  如何提高检测准确率？
A: 
1. 确保良好的光线条件
2. 定期更新级联分类器
3. 调整检测参数（缩放因子、邻域数等）

### Q: 数据如何备份？
A: 可使用MySQL自带的备份工具：
```bash
mysqldump -u root -p classroom_db > backup.sql
```

## 📝 开发计划

- [ ] 集成深度学习模型（更高准确率）
- [ ] 移动端应用（学生自我检查）
- [ ] 可视化仪表板
- [ ] 多摄像头支持
- [ ] 云端部署方案
- [ ] 教师移动端应用

## 🤝 贡献指南

欢迎提交Issue和Pull Request！

1. Fork本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 提交Pull Request

## 📄 许可证

MIT License - 详见 LICENSE 文件

## 📧 联系方式

- 项目维护者:  [@springbootAop](https://github.com/springbootAop)
- 反馈邮箱: springbootaop@example.com

## 🙏 致谢

感谢以下开源项目的支持：
- OpenCV
- Spring Boot
- Spring Framework
- Hibernate

---

**最后更新**:  2026-01-15

**版本**:  1.0.0

希望这个系统能帮助提升课堂教学效果！🎓

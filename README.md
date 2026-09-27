# 高中模拟器

基于 FastAPI 的高中校园文字模拟游戏后端。项目根据《高中模拟器文字游戏：后端架构与全套系统设计白皮书》实现，目前完成了可以独立运行和测试的核心规则层与 API 基础。

> 当前阶段为后端原型。它可以通过 Swagger UI 调用和体验，但尚未包含正式游戏前端。

## 当前状态

- 版本：后端原型 `2.0.0`
- 自动测试：6 项通过
- 内容数据：15 个主线事件、10 个成就
- 默认存储：SQLite
- 可选组件：PostgreSQL、Redis
- API 文档：启动后访问 `http://127.0.0.1:8000/docs`

## 已实现

- FastAPI 入口与接口文档
- SQLAlchemy 2.0 异步存档模型
- Redis 缓存与 Lua 分布式锁
- JSON 条件 DSL、动态事件权重与行动收益
- 考场三步策略和正态分布排位
- 三名 NPC 的送礼喜好与 40、80 点阶段锁
- 高考成绩和最高羁绊双轴结局矩阵
- 周末文具店、奶茶店、旧书摊与便利店打工
- 多周目遗产点和三种天资
- SSE 逐字毕业信
- 15 个主线事件和 10 个成就种子

## 运行

需要 Python 3.10 或更高版本。

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
python seed_database.py
uvicorn main:app --reload
```

打开 `http://127.0.0.1:8000/docs` 查看和调用接口。

默认数据库是当前目录下的 SQLite `game.db`，方便先运行。若需使用 PostgreSQL，可复制 `.env.example` 中的连接字符串并设置 `DATABASE_URL` 环境变量。Redis 相关模块已实现，但当前基础接口不会在启动时强制连接 Redis。

## 测试

```powershell
pip install -r requirements-dev.txt
pytest -q
```

## 已开放接口

| 方法 | 路径 | 功能 |
|---|---|---|
| `GET` | `/` | 服务状态 |
| `POST` | `/exam/calculate_result` | 根据三项考场策略计算成绩和校排名 |
| `POST` | `/social/send_gift` | NPC 送礼、好感变化和阶段锁 |
| `POST` | `/weekend/visit_location` | 周末商业街行动 |
| `POST` | `/ng_plus/start_new_game` | 使用遗产点选择天资并开始新周目 |
| `GET` | `/epilogue/stream_letter` | 通过 SSE 输出逐字毕业信 |

## 主要文件

| 文件 | 内容 |
|---|---|
| `database.py` | 异步数据库连接 |
| `models.py` | 玩家、事件、成就模型与存档结构 |
| `activity_engine.py` | 条件 DSL、动态权重、行动与属性钳制 |
| `exam_router.py` | 考场策略和排位接口 |
| `social_system.py` | NPC 送礼与阶段锁 |
| `ending_system.py` | 结局矩阵和遗产点计算 |
| `weekend_system.py` | 周末商业街 |
| `ng_plus_router.py` | 多周目开局 |
| `epilogue_sse.py` | 毕业信 SSE 流 |
| `seed_database.py` | 15 个主线事件与 10 个成就 |

## 与白皮书草稿相比的必要修复

- 修复了 Word 排版导致的 `ng_plus_router.py` 断行、缩进和 `initial_state` 拼写问题。
- 将接口的多个请求体参数整理为 Pydantic 请求对象，确保 FastAPI 可以直接生成正确接口文档。
- 补齐白皮书表格中已经列出的全部 15 个事件和 10 个成就。
- 对空条件、未知字段、属性上下限和未知地点增加了安全处理。
- CORS 在使用通配来源时关闭凭据，避免无效浏览器配置。

## 尚未扩展

- 真正调用大模型生成毕业信；目前按白皮书示例输出确定性纪念信。
- NPC 阶段契约任务的完整剧情处理。
- 李强救赎线的完整事件选择接口。
- 前端页面、账号系统、排行榜和生产部署配置。
- 河南院校志愿填报与录取数据库；可作为下一阶段接入。

更完整的完成情况与下一阶段边界见 [`STATUS.md`](STATUS.md)。

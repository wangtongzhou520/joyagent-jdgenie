# Genie-Tool 核心功能分析文档

## 项目概述

Genie-Tool 是一个基于 FastAPI 的智能工具服务平台，集成了多种AI工具和分析组件，为智能体系统提供丰富的工具支持。该项目实现了代码解释器、数据分析、深度搜索、报告生成、NL2SQL、TableRAG等核心功能，是 Genie 智能体系统的重要工具层。

## 技术架构

### 核心技术栈
- **FastAPI** - 现代高性能 Web 框架
- **Python 3.11+** - 编程语言
- **LiteLLM** - 多模型支持框架
- **smolagents** - 智能体工具框架
- **Pandas** - 数据处理
- **Scikit-learn** - 机器学习
- **Qdrant** - 向量数据库
- **Elasticsearch** - 全文检索
- **SQLite** - 轻量级数据库

### 项目结构
```
genie-tool/
├── genie_tool/                    # 核心模块
│   ├── api/                       # API 服务层
│   │   ├── tool.py               # 工具API接口
│   │   └── file_manage.py        # 文件管理API
│   ├── model/                     # 数据模型和协议
│   │   ├── protocal.py           # 请求/响应协议
│   │   ├── context.py            # 上下文模型
│   │   └── code.py               # 代码执行模型
│   ├── tool/                      # 工具实现层
│   │   ├── analysis_component/    # 数据分析组件
│   │   ├── search_component/      # 搜索组件
│   │   ├── table_rag/            # TableRAG组件
│   │   ├── code_interpreter.py   # 代码解释器
│   │   ├── auto_analysis.py      # 自动分析
│   │   ├── deepsearch.py         # 深度搜索
│   │   ├── nl2sql.py             # NL2SQL转换
│   │   └── report.py             # 报告生成
│   ├── db/                        # 数据库层
│   │   ├── db_engine.py          # 数据库引擎
│   │   └── file_table.py         # 文件表模型
│   └── util/                      # 工具类
├── server.py                      # FastAPI 服务器
├── .env_template                  # 环境变量模板
└── start.sh                       # 启动脚本
```

## 核心功能模块

### 1. 代码解释器 (Code Interpreter)

#### 1.1 功能特性
- **Python 代码执行**: 安全的 Python 代码执行环境
- **文件处理**: 支持多种文件格式的读取和处理
- **数据可视化**: 自动生成图表和可视化内容
- **结果输出**: 支持多种格式的结果输出

#### 1.2 核心实现
```python
@router.post("/code_interpreter")
async def post_code_interpreter(body: CIRequest):
    # 文件下载和处理
    # 代码执行环境准备
    # 智能体执行
    # 结果上传和返回
```

**技术特性**：
- 基于 smolagents 的智能体框架
- 沙箱化代码执行环境
- 流式输出支持
- 文件上传下载管理

#### 1.3 支持的数据格式
- **Excel**: .xlsx, .xls
- **CSV**: 逗号分隔值文件
- **JSON**: JSON格式数据
- **Text**: 纯文本文件
- **图片**: 数据可视化输出

### 2. 自动数据分析 (Auto Analysis)

#### 2.1 分析能力
- **描述性统计**: 基础统计信息分析
- **相关性分析**: 变量间关系分析
- **趋势分析**: 时间序列趋势识别
- **异常检测**: 数据异常值识别
- **预测建模**: 简单预测模型构建

#### 2.2 分析组件
```python
class AutoAnalysisAgent:
    # 数据获取工具
    tools = [GetDataTool, DataTransTool, InsightTool, SaveInsightTool, FinalAnswerTool]

    # 分析流程管理
    async def run_analysis(self, context: AnalysisContext)
```

**分析流程**：
1. **数据获取**: 从数据源获取分析数据
2. **数据预处理**: 数据清洗和转换
3. **统计分析**: 执行各种统计分析
4. **洞察提取**: 生成业务洞察
5. **结果保存**: 保存分析结果和可视化

#### 2.3 洞察类型
- **趋势洞察**: 数据变化趋势分析
- **分布洞察**: 数据分布特征分析
- **相关洞察**: 变量相关性分析
- **异常洞察**: 异常数据点识别
- **预测洞察**: 未来趋势预测

### 3. 深度搜索 (Deep Search)

#### 3.1 搜索引擎集成
- **Google SERP**: 通过 Serper API
- **Bing Search**: 微软搜索API
- **Jina Search**: Jina搜索服务
- **Sogou Search**: 搜狗搜索API

#### 3.2 搜索流程
```python
class DeepSearch:
    async def search(self, query: str, search_count: int = 10):
        # 1. 查询分解和优化
        # 2. 多引擎并行搜索
        # 3. 内容提取和清洗
        # 4. 相关性排序
        # 5. 答案生成
```

**搜索特性**：
- **查询分解**: 复杂查询的智能分解
- **并行搜索**: 多线程并行搜索
- **内容提取**: 网页内容智能提取
- **答案合成**: 多源信息综合分析

#### 3.3 内容处理
- **网页解析**: BeautifulSoup 网页内容提取
- **文本清洗**: 去除噪声和无关内容
- **相关性评分**: 基于语义相似度的内容排序
- **答案生成**: LLM驱动的答案合成

### 4. TableRAG (表格检索增强生成)

#### 4.1 核心功能
- **表格检索**: 基于语义的表格和字段检索
- **上下文增强**: 表格元数据和业务上下文
- **智能过滤**: 多阶段表格和字段筛选
- **SQL生成**: 基于检索结果的SQL生成

#### 4.2 检索架构
```python
class TableRAGAgent:
    # 向量检索
    qdrant_client: QdrantClient

    # ES检索
    es_client: Elasticsearch

    # 重排序
    reranker: BGEReranker
```

**检索流程**：
1. **查询理解**: 自然语言查询分析
2. **向量检索**: Qdrant向量相似度检索
3. **ES检索**: Elasticsearch全文检索
4. **表格过滤**: 基于业务规则的表格筛选
5. **字段过滤**: 细粒度字段筛选
6. **重排序**: 基于相关性的结果重排

#### 4.3 过滤策略
- **表级过滤**: 表名和表描述匹配
- **字段级过滤**: 字段名和字段描述匹配
- **业务过滤**: 业务提示词约束
- **相关性过滤**: 基于阈值的相关性筛选

### 5. NL2SQL (自然语言转SQL)

#### 5.1 转换能力
- **简单查询**: 基础SELECT查询
- **复杂查询**: 多表JOIN查询
- **聚合查询**: GROUP BY和聚合函数
- **条件查询**: WHERE条件和过滤
- **排序查询**: ORDER BY排序

#### 5.2 实现架构
```python
class NL2SQLAgent:
    async def nl2sql(self, query: str, schema_info: dict):
        # 1. 查询重写和标准化
        # 2. Schema检索和筛选
        # 3. SQL生成
        # 4. SQL验证和优化
```

**生成流程**：
1. **查询理解**: 自然语言意图分析
2. **Schema召回**: 相关表和字段识别
3. **SQL构建**: 基于模板的SQL生成
4. **语法检查**: SQL语法正确性验证
5. **优化建议**: SQL性能优化建议

### 6. 报告生成 (Report Generation)

#### 6.1 报告类型
- **HTML报告**: 交互式网页报告
- **Markdown报告**: 标准文档格式
- **PowerPoint**: PPT演示文稿
- **PDF报告**: 便携式文档格式

#### 6.2 模板系统
```python
def generate_report(template_type: str, data: dict, file_type: str):
    # 模板加载
    template = get_template(template_type)

    # 数据渲染
    content = template.render(data)

    # 格式转换
    return convert_to_format(content, file_type)
```

**模板特性**：
- **动态模板**: Jinja2模板引擎
- **样式定制**: 可定制的样式和主题
- **图表集成**: 自动图表生成和嵌入
- **多格式输出**: 统一模板多格式导出

### 7. 计划SOP (Plan Standard Operating Procedure)

#### 7.1 规划能力
- **任务分解**: 复杂任务的层次化分解
- **执行顺序**: 任务执行顺序规划
- **依赖管理**: 任务间依赖关系管理
- **资源分配**: 执行资源的合理分配

#### 7.2 SOP框架
```python
class PlanSOP:
    async def plan(self, goal: str, context: dict):
        # 1. 目标分析
        # 2. 任务分解
        # 3. 序列规划
        # 4. 依赖分析
        # 5. 执行计划生成
```

## API接口设计

### 1. 工具接口 (/v1/tool/)

#### 1.1 代码解释器接口
```http
POST /v1/tool/code_interpreter
Content-Type: application/json

{
    "requestId": "req_123",
    "task": "分析销售数据并生成图表",
    "fileNames": ["sales_data.xlsx"],
    "stream": true
}
```

#### 1.2 自动分析接口
```http
POST /v1/tool/auto_analysis
Content-Type: application/json

{
    "requestId": "req_124",
    "task": "分析用户行为数据",
    "dataUrl": "http://example.com/api/data",
    "analysisType": "trend_analysis"
}
```

#### 1.3 深度搜索接口
```http
POST /v1/tool/deep_search
Content-Type: application/json

{
    "requestId": "req_125",
    "query": "人工智能在医疗领域的应用",
    "searchEngine": "google",
    "maxResults": 10
}
```

#### 1.4 NL2SQL接口
```http
POST /v1/tool/nl2sql
Content-Type: application/json

{
    "requestId": "req_126",
    "query": "查询上个月销售额最高的产品",
    "schemaInfo": {...}
}
```

### 2. 文件管理接口 (/v1/file_tool/)

#### 2.1 文件上传接口
```http
POST /v1/file_tool/upload
Content-Type: multipart/form-data

file: [文件内容]
requestId: req_127
```

#### 2.2 文件预览接口
```http
GET /v1/file_tool/preview/{requestId}/{fileName}
```

#### 2.3 文件下载接口
```http
GET /v1/file_tool/download/{requestId}/{fileName}
```

## 配置和部署

### 1. 环境变量配置

#### 1.1 基础配置
```bash
# API密钥配置
OPENAI_API_KEY=your_openai_key
OPENAI_BASE_URL=https://api.openai.com/v1

# 模型配置
DEFAULT_MODEL=gpt-4o
CODE_INTEPRETER_MODEL=gpt-4o
ANALYSIS_MODEL=gpt-4o
```

#### 1.2 存储配置
```bash
# 文件系统
FILE_SAVE_PATH=file_db_dir
SQLITE_DB_PATH=autobots.db
FILE_SERVER_URL=http://127.0.0.1:1601/v1/file_tool
```

#### 1.3 搜索引擎配置
```bash
# 搜索引擎
USE_SEARCH_ENGINE=serp
SERPER_SEARCH_URL=https://google.serper.dev/search
SERPER_SEARCH_API_KEY=your_serper_key

SEARCH_COUNT=10
SEARCH_TIMEOUT=10
SEARCH_THREAD_NUM=5
```

#### 1.4 TableRAG配置
```bash
# 向量数据库
TR_QDRANT_URL=http://localhost:6333
TR_QDRANT_COLLECTION_NAME=table_schema
TR_QDRANT_LIMIT=30

# Elasticsearch
TR_ES_CONFIGS_HOST=localhost:9200
TR_ES_CONFIGS_INDEX=table_index
```

### 2. 部署方式

#### 2.1 开发环境
```bash
# 1. 依赖安装
pip install uv
cd genie-tool
uv sync
source .venv/bin/activate

# 2. 数据库初始化
python -m genie_tool.db.db_engine

# 3. 环境配置
cp .env_template .env
# 填写环境变量

# 4. 启动服务
uv run python server.py
```

#### 2.2 生产环境
```bash
# 使用多进程部署
uvicorn server:app --host 0.0.0.0 --port 1601 --workers 10

# 或使用启动脚本
./start.sh
```

#### 2.3 Docker部署
```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY . .

RUN pip install uv && uv sync

EXPOSE 1601

CMD ["uv", "run", "python", "server.py"]
```

## 中间件和工具类

### 1. 中间件设计

#### 1.1 异常处理中间件
```python
class UnknownException:
    # 统一异常处理
    # 错误日志记录
    # 用户友好错误信息
```

#### 1.2 性能监控中间件
```python
class HTTPProcessTimeMiddleware:
    # 请求处理时间统计
    # 性能指标收集
    # 慢请求告警
```

#### 1.3 CORS中间件
```python
CORSMiddleware:
    allow_origins=["*"]
    allow_methods=["*"]
    allow_headers=["*"]
    allow_credentials=True
```

### 2. 工具类模块

#### 2.1 文件工具 (file_util.py)
- **文件上传**: 多格式文件上传处理
- **文件下载**: 批量文件下载管理
- **格式转换**: 文件格式转换支持
- **存储管理**: 本地存储路径管理

#### 2.2 LLM工具 (llm_util.py)
- **模型调用**: 统一的LLM调用接口
- **提示词管理**: 动态提示词生成
- **流式处理**: 流式响应处理
- **错误重试**: 自动重试机制

#### 2.3 日志工具 (log_util.py)
- **性能计时**: 函数执行时间统计
- **结构化日志**: 统一日志格式
- **日志轮转**: 自动日志文件管理
- **调试支持**: 开发调试功能

## 数据模型设计

### 1. 请求模型

#### 1.1 基础请求模型
```python
class CIRequest(BaseModel):
    request_id: str                     # 请求ID
    task: Optional[str]                 # 任务描述
    file_names: Optional[List[str]]     # 输入文件列表
    stream: bool = True                 # 是否流式响应
    stream_mode: Optional[StreamMode]   # 流式模式配置
```

#### 1.2 专用请求模型
```python
class ReportRequest(CIRequest):
    file_type: Literal["html", "markdown", "ppt"]  # 报告类型
    template_type: str                             # 模板类型

class AutoAnalysisRequest(CIRequest):
    analysis_type: str                             # 分析类型
    data_source: str                              # 数据源

class DeepSearchRequest(BaseModel):
    query: str                                    # 搜索查询
    search_engine: str                            # 搜索引擎
    max_results: int                              # 最大结果数
```

### 2. 响应模型

#### 2.1 流式响应
```python
class ActionOutput(BaseModel):
    action_type: str                    # 动作类型
    content: str                        # 内容
    timestamp: datetime                 # 时间戳
    status: str                         # 状态

class CodeOutput(BaseModel):
    code: str                          # 执行代码
    output: str                        # 执行结果
    error: Optional[str]               # 错误信息
    files: List[str]                   # 生成文件
```

### 3. 上下文模型

#### 3.1 分析上下文
```python
class AnalysisContext(BaseModel):
    request_id: str                    # 请求ID
    task: str                          # 分析任务
    data_schema: dict                  # 数据结构
    insights: List[dict]               # 分析洞察
    artifacts: List[str]               # 生成产物
```

## 性能优化

### 1. 并发处理
- **异步编程**: 基于 asyncio 的异步处理
- **线程池**: CPU密集型任务的线程池处理
- **连接池**: 数据库和HTTP连接池管理
- **缓存机制**: 多级缓存减少重复计算

### 2. 资源管理
- **内存优化**: 大文件流式处理
- **临时文件**: 自动临时文件清理
- **进程隔离**: 代码执行环境隔离
- **资源限制**: CPU和内存使用限制

### 3. 性能监控
- **响应时间**: API响应时间监控
- **吞吐量**: 请求处理吞吐量统计
- **资源使用**: CPU、内存使用监控
- **错误率**: 错误请求比例监控

## 安全性设计

### 1. 代码执行安全
- **沙箱环境**: 隔离的代码执行环境
- **权限限制**: 文件系统访问权限限制
- **网络隔离**: 限制外部网络访问
- **资源限制**: CPU和内存使用限制

### 2. 数据安全
- **敏感词过滤**: 自动敏感信息过滤
- **文件访问控制**: 基于request_id的文件隔离
- **数据加密**: 敏感数据加密存储
- **访问日志**: 完整的访问日志记录

### 3. API安全
- **输入验证**: 严格的参数验证
- **SQL注入防护**: 参数化查询防护
- **文件上传安全**: 文件类型和大小限制
- **速率限制**: API调用频率限制

## 扩展性设计

### 1. 工具扩展
- **插件架构**: 基于接口的工具插件
- **动态加载**: 运行时工具加载
- **配置驱动**: 外部配置驱动的工具启用
- **版本管理**: 工具版本兼容性管理

### 2. 模型扩展
- **多模型支持**: LiteLLM多模型适配
- **模型路由**: 基于任务的模型路由
- **负载均衡**: 多模型实例负载均衡
- **降级策略**: 模型不可用时的降级策略

### 3. 存储扩展
- **多存储后端**: 支持多种存储系统
- **数据分片**: 大数据的分片存储
- **备份恢复**: 自动备份和恢复机制
- **数据迁移**: 存储系统间数据迁移

## 应用场景

### 1. 数据科学分析
- **探索性数据分析**: 快速数据探索和可视化
- **统计建模**: 自动统计模型构建
- **报告生成**: 自动化分析报告生成
- **异常检测**: 数据质量和异常监控

### 2. 商业智能
- **业务分析**: 业务指标分析和洞察
- **趋势预测**: 业务趋势预测分析
- **竞品分析**: 市场和竞品情报分析
- **决策支持**: 数据驱动的决策支持

### 3. 内容创作
- **报告撰写**: 自动化报告和文档生成
- **数据可视化**: 自动图表和可视化生成
- **内容搜索**: 深度信息搜索和整理
- **知识整合**: 多源信息的整合分析

### 4. 开发支持
- **代码分析**: 代码质量分析和优化建议
- **文档生成**: 自动代码文档生成
- **测试支持**: 自动化测试用例生成
- **性能分析**: 代码性能分析和优化

## 总结

Genie-Tool 是一个功能丰富、架构完善的智能工具服务平台，具有以下核心优势：

1. **功能完整**: 覆盖数据分析、搜索、SQL生成、报告等核心场景
2. **技术先进**: 基于最新的AI技术和工具框架
3. **架构优雅**: 模块化设计，易于扩展和维护
4. **性能优异**: 异步处理，支持高并发和大数据处理
5. **安全可靠**: 完善的安全机制和错误处理
6. **部署简单**: 标准化的配置和部署流程

该项目为 Genie 智能体系统提供了强大的工具支持能力，通过丰富的API接口和灵活的配置机制，能够满足各种复杂的业务场景需求。无论是数据分析、内容创作，还是决策支持，都能提供专业的工具服务。
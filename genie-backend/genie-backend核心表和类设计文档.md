# Genie-Backend 核心表和类设计文档

## 数据库表设计

### 1. 核心业务表

#### 1.1 chat_model_info - 数据模型信息表

**表结构**:
```sql
CREATE TABLE `chat_model_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `code` varchar(50) NOT NULL COMMENT '模型编码',
  `type` varchar(10) NOT NULL COMMENT '模型类型TABLE,SQL',
  `name` varchar(100) DEFAULT NULL COMMENT '模型名称',
  `content` text NOT NULL COMMENT '模型内容，表或者sql',
  `use_prompt` text COMMENT '模型使用说明',
  `business_prompt` text COMMENT '模型业务限定提示词',
  `yn` tinyint(2) NOT NULL DEFAULT '1' COMMENT '是否有效',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='数据模型表信息';
```

**字段说明**:
- `id`: 主键，自增长
- `code`: 模型编码，用于唯一标识数据模型
- `type`: 模型类型，支持 TABLE（表模型）和 SQL（SQL模型）
- `name`: 模型名称，用于显示和识别
- `content`: 模型内容，存储表名或SQL语句
- `use_prompt`: 模型使用说明，指导如何使用该模型
- `business_prompt`: 业务限定提示词，用于约束模型的使用范围
- `yn`: 逻辑删除标志，1为有效，0为删除

**业务用途**:
- 存储智能问数系统中的数据模型定义
- 支持表模型和SQL模型两种类型
- 为NL2SQL提供模型元数据

#### 1.2 chat_model_schema - 数据模型字段信息表

**表结构**:
```sql
CREATE TABLE `chat_model_schema` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `model_code` varchar(200) NOT NULL COMMENT '模型编码',
  `column_id` varchar(1000) NOT NULL COMMENT '字段唯一ID',
  `column_name` varchar(200) NOT NULL COMMENT '字段中文名',
  `column_comment` varchar(1000) NOT NULL COMMENT '字段描述',
  `few_shot` text COMMENT '值枚举逗号分隔',
  `data_type` varchar(20) DEFAULT NULL COMMENT '字段值类型',
  `synonyms` varchar(300) DEFAULT NULL COMMENT '同义词',
  `vector_uuid` varchar(400) DEFAULT NULL COMMENT '向量库数据id',
  `default_recall` tinyint(2) NOT NULL DEFAULT '0' COMMENT '默认召回',
  `analyze_suggest` tinyint(2) NOT NULL DEFAULT '0' COMMENT '分析建议0可选，-1禁止用于分析维度，1建议',
  `yn` tinyint(2) NOT NULL DEFAULT '1' COMMENT '是否有效',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='数据模型字段信息';
```

**字段说明**:
- `id`: 主键，自增长
- `model_code`: 关联的模型编码，外键关联chat_model_info表
- `column_id`: 字段唯一标识符
- `column_name`: 字段中文名称
- `column_comment`: 字段详细描述，用于语义理解
- `few_shot`: 字段值的示例枚举，逗号分隔
- `data_type`: 字段数据类型（varchar、int、date等）
- `synonyms`: 字段同义词，用于扩展语义匹配
- `vector_uuid`: 向量数据库中的数据ID，用于向量检索
- `default_recall`: 是否默认召回该字段
- `analyze_suggest`: 分析建议级别（-1禁止、0可选、1建议）
- `yn`: 逻辑删除标志

**业务用途**:
- 存储数据模型的详细字段信息
- 支持TableRAG的字段召回功能
- 为向量检索提供字段语义信息
- 指导NL2SQL的字段选择

#### 1.3 sales_data - 示例销售数据表

**表结构**:
```sql
CREATE TABLE sales_data (
    row_id INT PRIMARY KEY COMMENT '行 ID',
    order_id VARCHAR(50) DEFAULT NULL COMMENT '订单 ID',
    order_date DATE COMMENT '订单日期',
    ship_date DATE COMMENT '发货日期',
    ship_mode VARCHAR(50) DEFAULT NULL COMMENT '邮寄方式',
    customer_id VARCHAR(50) DEFAULT NULL COMMENT '客户 ID',
    customer_name VARCHAR(100) DEFAULT NULL COMMENT '客户名称',
    segment VARCHAR(50) DEFAULT NULL COMMENT '细分',
    city VARCHAR(100) DEFAULT NULL COMMENT '城市',
    state_province VARCHAR(100) DEFAULT NULL COMMENT '省/自治区',
    country VARCHAR(100) DEFAULT NULL COMMENT '国家',
    region VARCHAR(50) DEFAULT NULL COMMENT '地区',
    product_id VARCHAR(50) DEFAULT NULL COMMENT '产品 ID',
    category VARCHAR(50) DEFAULT NULL COMMENT '产品类别',
    sub_category VARCHAR(50) DEFAULT NULL COMMENT '产品子类别',
    product_name VARCHAR(255) DEFAULT NULL COMMENT '产品名称',
    sales DECIMAL(10, 4) DEFAULT NULL COMMENT '销售额',
    quantity INT DEFAULT NULL COMMENT '销售数量',
    discount DECIMAL(10, 4) DEFAULT NULL COMMENT '折扣',
    profit DECIMAL(10, 4) DEFAULT NULL COMMENT '利润'
) COMMENT='销售数据表';
```

**业务用途**:
- 作为智能问数的示例数据源
- 展示系统对复杂业务数据的处理能力
- 支持多维度数据分析查询

### 2. 表关系设计

```
chat_model_info (1) ←→ (N) chat_model_schema
    ↓ (model_code)
chat_model_schema.model_code → chat_model_info.code
```

**关系说明**:
- 一个数据模型可以包含多个字段信息
- 通过model_code字段建立关联关系
- 支持模型和字段的级联查询

## 核心类设计

### 1. 实体类设计

#### 1.1 ChatModelInfo - 数据模型实体类

```java
@Data
@TableName("chat_model_info")
public class ChatModelInfo implements Serializable {
    private static final long serialVersionUID = 8763697882256572393L;

    private Long id;                    // 主键
    private String code;                // 模型编码
    private String type;                // 模型类型
    private String content;             // 模型内容
    private String name;                // 模型名称
    private String usePrompt;           // 使用说明
    private String businessPrompt;      // 业务提示词
    @TableLogic
    private Integer yn;                 // 逻辑删除标志
}
```

**设计特点**:
- 使用Lombok简化代码
- 支持MyBatis-Plus的逻辑删除
- 实现Serializable接口支持序列化

#### 1.2 ChatModelSchema - 字段信息实体类

```java
@Data
@TableName("chat_model_schema")
public class ChatModelSchema implements Serializable {
    private static final long serialVersionUID = -6284827149526794290L;

    private Long id;                    // 主键
    private String modelCode;           // 模型编码
    private String columnId;            // 字段ID
    private String columnName;          // 字段名称
    private String columnComment;       // 字段描述
    private String fewShot;             // 示例值
    private String dataType;            // 数据类型
    private String synonyms;            // 同义词
    private String vectorUuid;          // 向量ID
    private int defaultRecall;          // 默认召回
    private int analyzeSuggest;         // 分析建议
    @TableLogic
    private Integer yn;                 // 逻辑删除标志
}
```

### 2. 数据访问层设计

#### 2.1 Mapper接口设计

```java
@Mapper
public interface ChatModelInfoMapper extends BaseMapper<ChatModelInfo> {
    // 继承MyBatis-Plus的BaseMapper，提供基础CRUD操作
    // 可扩展自定义查询方法
}

@Mapper
public interface ChatModelSchemaMapper extends BaseMapper<ChatModelSchema> {
    // 继承MyBatis-Plus的BaseMapper，提供基础CRUD操作
    // 可扩展字段级别的查询方法
}
```

**设计优势**:
- 使用MyBatis-Plus简化开发
- 提供完整的CRUD操作
- 支持条件构造器查询
- 自动分页功能

### 3. 智能体核心类设计

#### 3.1 BaseAgent - 智能体基类

```java
@Slf4j
@Data
@Accessors(chain = true)
public abstract class BaseAgent {

    // 核心属性
    private String name;                        // 智能体名称
    private String description;                 // 描述信息
    private String systemPrompt;                // 系统提示词
    private String nextStepPrompt;              // 下一步提示词
    public ToolCollection availableTools;      // 可用工具集合
    private Memory memory;                      // 记忆模块
    protected LLM llm;                         // 大语言模型
    protected AgentContext context;            // 执行上下文

    // 执行控制
    private AgentState state;                  // 智能体状态
    private int maxSteps;                      // 最大执行步数
    private int currentStep;                   // 当前步数
    private int duplicateThreshold;            // 重复阈值

    // 输出控制
    Printer printer;                           // 输出打印器

    // 抽象方法
    public abstract void run();                // 执行方法
    protected abstract void processStep();     // 处理步骤
}
```

**设计特点**:
- 抽象基类定义通用功能
- 状态管理和执行控制
- 工具集成和记忆管理
- 链式调用支持

#### 3.2 AgentContext - 智能体上下文

```java
@Data
@Builder
public class AgentContext {
    private String requestId;              // 请求ID
    private String sessionId;              // 会话ID
    private String query;                  // 查询内容
    private String task;                   // 任务描述
    private Set<Tool> tools;               // 工具集合
    private Printer printer;               // 输出打印器
    private List<String> productFiles;     // 产品文件列表
}
```

**设计优势**:
- 封装执行上下文信息
- 支持Builder模式构建
- 提供完整的执行环境

### 4. 数据模型类设计

#### 4.1 SqlModel - SQL模型类

```java
@Data
public class SqlModel {
    private List<String> selectColumns;       // 选择字段
    private List<FromTable> fromTables;       // 数据表
    private List<WhereCondition> whereConditions; // 查询条件
    private List<DataOrderBy> orderByList;    // 排序条件
    private PageObject pageObject;            // 分页信息
}
```

#### 4.2 ModelColumn - 模型字段类

```java
@Data
public class ModelColumn {
    private String columnId;               // 字段ID
    private String columnName;             // 字段名称
    private String columnComment;          // 字段描述
    private String dataType;               // 数据类型
    private List<String> enumValues;       // 枚举值
    private boolean isRequired;            // 是否必需
}
```

#### 4.3 WhereCondition - 查询条件类

```java
@Data
public class WhereCondition {
    private String columnId;               // 字段ID
    private ComparisonType operator;       // 比较操作符
    private Object value;                  // 条件值
    private String logicalOperator;        // 逻辑操作符(AND/OR)
}
```

### 5. 服务层设计模式

#### 5.1 服务接口设计

```java
@Service
public class DataAgentService {

    // NL2SQL核心服务
    public void chatQuery(DataAgentChatReq req, SseEmitter emitter);

    // Schema召回服务
    public List<ChatModelSchema> recallSchema(String query, String modelCode);

    // 模型管理服务
    public List<ModelInfoDto> getAllModels();

    // 数据预览服务
    public QueryResult previewData(String modelCode);
}
```

#### 5.2 工厂模式应用

```java
@Component
public class AgentHandlerFactory {

    private final Map<String, AgentResponseHandler> handlers;

    public AgentResponseHandler getHandler(String agentType) {
        return handlers.get(agentType);
    }
}
```

## 设计模式应用

### 1. 工厂模式
- **AgentHandlerFactory**: 智能体处理器工厂
- **JdbcConnectionFactory**: 数据库连接工厂
- **DialectFactory**: 数据库方言工厂

### 2. 策略模式
- **JdbcDialect**: 不同数据库的SQL方言策略
- **AgentResponseHandler**: 不同智能体的响应处理策略

### 3. 建造者模式
- **AgentContext**: 使用Builder模式构建复杂对象
- **SqlModel**: SQL查询对象的构建

### 4. 模板方法模式
- **BaseAgent**: 定义智能体执行模板
- **BaseAgentResponseHandler**: 定义响应处理模板

### 5. 观察者模式
- **Printer**: 输出事件的发布订阅
- **SSE流式传输**: 数据变化的实时推送

## 数据库访问层架构

### 1. MyBatis-Plus集成
- **BaseMapper**: 提供基础CRUD操作
- **条件构造器**: 动态SQL构建
- **分页插件**: 自动分页处理
- **逻辑删除**: 软删除支持

### 2. 多数据源支持
- **JDBC抽象层**: 统一的数据访问接口
- **连接池管理**: HikariCP高性能连接池
- **事务管理**: Spring事务注解支持

### 3. 数据库方言
- **MySQLDialect**: MySQL特定语法
- **ClickHouseDialect**: ClickHouse特定语法
- **H2Dialect**: H2数据库特定语法

## 缓存设计

### 1. 本地缓存
- **模型元数据缓存**: 缓存频繁访问的模型信息
- **Schema缓存**: 缓存字段映射关系
- **向量缓存**: 缓存向量检索结果

### 2. 分布式缓存
- **Redis集成**: 支持分布式缓存
- **缓存策略**: LRU淘汰策略
- **缓存更新**: 支持主动更新和过期更新

## 安全设计

### 1. SQL注入防护
- **参数化查询**: 使用预编译语句
- **SQL解析**: 对用户输入进行安全检查
- **白名单过滤**: 限制可执行的SQL操作

### 2. 数据访问控制
- **权限验证**: 接口级别的权限控制
- **数据脱敏**: 敏感数据的脱敏处理
- **审计日志**: 完整的操作审计记录

## 性能优化设计

### 1. 查询优化
- **索引设计**: 合理的数据库索引
- **查询缓存**: 热点查询结果缓存
- **批量操作**: 减少数据库交互次数

### 2. 内存管理
- **对象池**: 重用频繁创建的对象
- **垃圾回收**: 优化GC性能
- **内存监控**: 实时内存使用监控

## 扩展性设计

### 1. 插件化架构
- **工具插件**: 支持自定义工具扩展
- **智能体插件**: 支持新智能体类型
- **数据源插件**: 支持新数据源类型

### 2. 配置驱动
- **外部配置**: 支持外部配置文件
- **动态配置**: 支持运行时配置更新
- **环境隔离**: 支持多环境配置

这个设计文档全面展示了Genie-Backend项目的核心表结构和类设计，体现了现代企业级Java应用的设计理念和最佳实践。
# 学院AI辅导员系统（跨平台版）

在微信公众号嵌入智能客服板块，学生通过对话获取学校文件和办事流程，AI解答基础问题，复杂问题转接老师。本版本支持 Windows、macOS、Linux 三平台运行。

## 项目简介

本项目是 AI 辅导员系统的跨平台发行版，在主项目基础上增加了多平台兼容性支持、一键安装脚本、知识导入管理工具、学校公告自动抓取等功能。适用于需要在不同操作系统上部署的场景。

## 功能特性

- **微信公众号接入** - 接收学生消息，回复AI结果
- **RAG知识库** - 学校文件PDF/Word解析，向量存储，支持检索
- **意图识别** - 判断学生问题类型（奖助学金/宿舍/消三比/其他）
- **AI解答** - 简单问题基于知识库直接回答（xAI API + Fallback机制）
- **转人工** - 复杂问题推送老师联系方式（姓名+电话+工单）
- **知识库管理** - 增删学校文件和办事流程文档
- **自动抓取** - 定时从学校官网抓取最新公告
- **统一导入** - 支持URL/文件/RSS/JSON等多种方式导入知识
- **跨平台** - 支持Windows、macOS、Linux

## 技术栈

| 组件 | 技术 |
|------|------|
| 后端框架 | Python 3.8+ / FastAPI |
| 向量数据库 | Chroma |
| 嵌入模型 | sentence-transformers |
| 大语言模型 | xAI Grok API |
| 微信接入 | 微信公众号 API |
| 网页抓取 | beautifulsoup4 / requests |
| 文档解析 | pypdf / python-docx |

## 项目结构

```
ai-tutor-crossplatform/
├── app/
│   ├── ai/
│   │   ├── grok_client.py          # xAI Grok API客户端
│   │   └── response_generator.py    # AI回复生成器（RAG+Fallback）
│   ├── intent/
│   │   └── recognizer.py            # 意图识别（6种意图）
│   ├── rag/
│   │   ├── knowledge_base.py        # Chroma向量知识库（跨平台）
│   │   └── document_parser.py       # PDF/Word文档解析
│   ├── wechat/
│   │   └── handler.py               # 微信公众号接入处理
│   ├── platform.py                  # 跨平台兼容性工具
│   ├── config.py
│   └── main.py                      # FastAPI主应用
├── config.yaml                      # 配置文件
├── requirements.txt                 # Python依赖
├── start.sh                         # macOS/Linux启动脚本
├── start.bat                        # Windows启动脚本
├── install_dependencies.py          # 跨平台一键安装脚本
├── import_manager.py                # 命令行知识导入工具
├── import_api.py                    # 知识导入HTTP API服务（端口8001）
├── school_news_fetcher.py           # 学校公告自动抓取工具
├── manage_knowledge.py              # 知识库管理工具
└── knowledge/                       # 知识库文档目录
    └── uploads/
```

## 使用方法

### Windows安装

**方法1：一键安装（推荐）**

1. 下载并解压本文件夹
2. 双击运行 `install_dependencies.py`
3. 等待依赖安装完成

**方法2：手动安装**

```bash
pip install -r requirements.txt
# 双击 start.bat 或运行：
python start.bat
```

### macOS安装

```bash
python3 install_dependencies.py
# 或手动安装：
pip3 install -r requirements.txt
chmod +x start.sh
./start.sh
```

### Linux安装

```bash
sudo apt-get install python3 python3-pip
python3 install_dependencies.py
# 或手动安装：
pip3 install -r requirements.txt
chmod +x start.sh
./start.sh
```

### 配置

编辑 `config.yaml`，填入 xAI API Key 和微信公众号配置：

```yaml
xai:
  api_key: "your-xai-api-key"

wechat:
  app_id: "your-wechat-appid"
  app_secret: "your-wechat-appsecret"
  token: "your-wechat-token"
  encoding_aes_key: "your-wechat-aes-key"
```

服务启动后访问: http://localhost:8000

## 管理工具

### import_manager.py - 命令行知识导入

```bash
python import_manager.py status                    # 查看状态
python import_manager.py url "https://学校.edu/公告"  # 从URL导入
python import_manager.py file 文档.pdf               # 导入文件
python import_manager.py batch ./知识文件夹/           # 批量导入
python import_manager.py announce 公告.json           # 导入公告
python import_manager.py teacher teachers.json       # 更新老师信息
python import_manager.py source --fetch              # 抓取所有数据源
```

### import_api.py - HTTP API服务（端口8001）

```bash
python import_api.py                                 # 启动API服务
curl "http://localhost:8001/api/import/url?url=https://..."  # 导入网页
curl -X POST -F "file=@文档.pdf" http://localhost:8001/api/import/file  # 上传文件
```

### manage_knowledge.py - 知识库管理

```bash
python manage_knowledge.py status                    # 查看状态
python manage_knowledge.py list                      # 列出所有知识
python manage_knowledge.py add-scholarship -c "内容"  # 添加知识
python manage_knowledge.py export -f backup.json     # 导出备份
python manage_knowledge.py interactive               # 交互式管理
```

### school_news_fetcher.py - 公告自动抓取

```bash
python school_news_fetcher.py --test                 # 测试配置
python school_news_fetcher.py                        # 抓取并更新
python school_news_fetcher.py --dry-run              # 预览模式
```

## 故障排除

- **Python未找到**: Windows从python.org下载；macOS用 `brew install python@3.11`；Linux用 `apt-get install python3`
- **依赖安装失败**: 先运行 `pip install --upgrade pip` 再重试
- **模型加载失败**: 设置 `export HF_ENDPOINT="https://hf-mirror.com"` 后重试
- **端口被占用**: 修改 config.yaml 端口号，或用 `lsof -i :8000` 查找占用进程

## License

MIT

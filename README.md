## :shield: Swagger 未授权检测工具

用于探测 Swagger/OpenAPI 文档并尝试未授权访问接口，辅助安全自查。识别 swagger-ui 页面、swagger-resources 入口、Swagger 1.x/V2/V3 文档，在谨慎策略下对接口进行 GET/POST 探测并生成 Excel 报告。

![Mode](https://img.shields.io/badge/mode-single%20%7C%20batch-blue)
![Report](https://img.shields.io/badge/report-Excel-success)
![Lang](https://img.shields.io/badge/lang-Python%203.8%2B-informational)
![Safe](https://img.shields.io/badge/scope-GET%2FPOST-yellow)

---

### :sparkles: 核心特性
- **多入口与版本解析**
  - 识别 `openapi.json`/`swagger.json`（V2/V3）、`swagger-resources`、Swagger 1.x 资源列表
  - 可从 swagger-ui HTML 页面中解析真实 API 文档地址（ Selenium 渲染）
  
- **安全与克制**
  - 仅发起 `GET`/`POST` 请求，默认忽略破坏性动作
  
  - 路径命中危险关键字（如  'delete', 'remove', 'destroy', 'drop', 'del', 'erase', 'update', 'modify', 'edit', 'set', 'change', 'upd',
  
      'mod', 'patch', 'put', 'add', 'create', 'new', 'insert', 'save', 'upload'）自动过滤
  
  - 对 HTML/二进制/非文本进行类型判定并“降噪”保存
  
- **报告输出**
  
  - 生成 Excel 报告：`所有API`、`已调用API`、`已过滤API`
  - 自动调宽与状态配色，高可读
  
- **实用开关**
  - `--force-domain` 强制使用初始域名，忽略文档内 `servers`

---

### :file_folder: 目录结构
- `swagger.py`：核心扫描逻辑与 CLI
- `chromedriver.exe`：Windows ChromeDriver
- `debug.log`：调试日志（运行生成）

---

### :rocket: 快速开始
```bash
# 单目标扫描（自动识别入口类型：swagger-ui / api-docs / swagger-resources）
python swagger.py -u https://example.com/swagger-ui/index.html


# 忽略文档内 servers，强制使用初始域名（遇到内网域名或IP直接使用此参数）
python swagger.py -u https://example.com/v3/api-docs --force-domain
```

```bash
# 批量扫描（每行一个 URL）
# urls.txt:
# https://a.com/v3/api-docs
# https://b.com/swagger-ui/index.html
python swagger.py -f urls.txt
```
红框或者error忽略即可
<img width="2560" height="1528" alt="image" src="https://github.com/user-attachments/assets/9a183f81-d1f4-4010-ab3a-9209ea2e06a5" />
<img width="2560" height="1528" alt="image" src="https://github.com/user-attachments/assets/28e799df-e7d9-4919-af72-e1efa3ef9d78" />
<img width="2560" height="262" alt="image" src="https://github.com/user-attachments/assets/974553a7-f89b-4257-93a0-c79622e79b00" />
<img width="2560" height="1192" alt="image" src="https://github.com/user-attachments/assets/c719a226-daa0-49b2-98c4-3ef791762158" />
<img width="2560" height="1192" alt="image" src="https://github.com/user-attachments/assets/5b367756-6250-4b8b-94c5-7b79e333b684" />
<img width="2560" height="1192" alt="image" src="https://github.com/user-attachments/assets/6d1315cb-d018-45b8-94db-95acabf8074a" />
设计的execl更方便查缺补漏


### :package: 安装与依赖

- Python 3（本人Python 3.10.11）
- 依赖安装：
```bash
pip install requests loguru selenium openpyxl urllib3
```
- 若需要解析 swagger-ui HTML：
  - 安装 Chrome 与匹配版本 ChromeDriver（一般都用chrome问题不大）
  - 将驱动置于项目根目录（Windows 可用 `chromedriver.exe`，其他平台放置 `chromedriver` 并赋执行权限）测试过win+linux没问题，mac都自己试下
  - chromedriver建议都自己下载，避免钓鱼，py文件同目录下即可；
  - 官网地址附上：https://googlechromelabs.github.io/chrome-for-testing/

---

### :gear: 使用说明

- 常用参数
  - `-u, --url`：单个目标（支持 swagger-ui、api-docs、swagger-resources）
  - `-f, --file`：批量目标文件（逐行 URL）
  - `--force-domain`：忽略文档内 `servers`，强制使用初始域名

- 请求与参数策略（宁可漏报也不可误删误改）
  - 仅尝试 `GET`、`POST`
  - 自动填充占位参数：
    - path 参数：用占位值替换路径变量（数字就填充1，字母填充a）
    - query 参数：填充简单占位值
    - body(JSON)：按 schema 尝试生成示例
  - 命中高风险关键字的 URL 自动跳过

---

### :memo: 报告说明
- 扫描结束生成 `YYYYMMDDHHMM_xxx.xlsx`：
  - `所有API`：记录发现的所有路径，标注“调用/过滤”
  - `已调用API`：记录请求方法、URL、参数、状态码、截断后的响应
  - `已过滤API`：记录被策略拦截的接口与原因

---

### :memo: 参考
  - 感谢evilc0deooo师傅，在此项目基础上二开，新增过滤与输出需求，兼容1/2/3.0
  - https://github.com/evilc0deooo/SwaggerHound

---

### :warning: 风险与合规
- 仅用于授权范围内的自查与安全验证
- 使用者需自行承担使用与合规责任

---

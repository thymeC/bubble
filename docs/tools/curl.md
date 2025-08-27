### **cURL 原理与语法简介**

cURL（Client URL）是一个 **命令行工具** 和 **库**（libcurl），用于通过 **各种协议**（如 HTTP、HTTPS、FTP、SFTP 等）传输数据。它的核心功能是 **发送请求并接收响应**，常用于测试 API、下载文件、调试网络等。

---

## **1. cURL 工作原理**
1. **解析 URL**：cURL 解析你提供的 URL，确定协议（如 `http://`、`ftp://`）、主机名、端口、路径等。
2. **建立连接**：根据协议，cURL 使用 TCP/IP 建立与目标服务器的连接（如 HTTP 默认端口 80，HTTPS 默认 443）。
3. **发送请求**：构造 HTTP 请求（如 `GET`、`POST`），并发送请求头和请求体（如果有）。
4. **接收响应**：服务器处理请求后返回响应，cURL 接收并显示在终端或保存到文件。
5. **关闭连接**：完成数据传输后，cURL 关闭连接（除非使用 `--keepalive` 保持长连接）。

---

## **2. 基本语法**
```bash
curl [选项] [URL]
```

### **常用选项**
| 选项 | 说明 |
|------|------|
| `-X <METHOD>` | 指定 HTTP 方法（如 `GET`, `POST`, `PUT`, `DELETE`） |
| `-H "Header: Value"` | 添加请求头（如 `-H "Content-Type: application/json"`） |
| `-d 'data'` | 发送 POST 请求数据（如 `-d '{"name":"John"}'`） |
| `-o <file>` | 下载文件并保存（如 `-o output.txt`） |
| `-O` | 下载文件并使用远程文件名 |
| `-L` | 跟随重定向（自动跳转） |
| `-v` | 显示详细请求/响应信息（调试用） |
| `-u user:pass` | 基本认证（如 `-u admin:123456`） |
| `--data-urlencode` | URL 编码 POST 数据 |
| `-k` | 忽略 SSL 证书验证（不安全，仅测试用） |
| `-s` | 静默模式（不显示进度/错误信息） |

---

## **3. 常见用法示例**
### **(1) 发送 GET 请求**
```bash
curl https://api.example.com/users
```

### **(2) 发送 POST 请求（带 JSON 数据）**
```bash
curl -X POST https://api.example.com/users \
     -H "Content-Type: application/json" \
     -d '{"name": "John", "age": 30}'
```

### **(3) 下载文件**
```bash
curl -O https://example.com/file.zip  # 保存为 file.zip
curl -o custom_name.zip https://example.com/file.zip  # 自定义文件名
```

### **(4) 带认证的请求**
```bash
curl -u username:password https://api.example.com/protected
```

### **(5) 调试模式（显示请求头）**
```bash
curl -v https://google.com
```

### **(6) 模拟表单提交**
```bash
curl -X POST https://example.com/login \
     -H "Content-Type: application/x-www-form-urlencoded" \
     --data-urlencode "user=admin" \
     --data-urlencode "pass=123456"
```

---

## **4. 高级用法**
### **(1) 使用 Cookie**
```bash
curl -b "cookie_name=value" https://example.com
```
或从文件读取 Cookie：
```bash
curl -b cookies.txt https://example.com
```

### **(2) 上传文件**
```bash
curl -X POST -F "file=@/path/to/file.jpg" https://example.com/upload
```

### **(3) 限速下载**
```bash
curl --limit-rate 100K -O https://example.com/largefile.zip
```

### **(4) 测试 API 并格式化 JSON 响应**
```bash
curl -s https://api.example.com/data | jq
```
（需要安装 `jq` 工具）

---

## **5. 总结**
- **cURL 是一个强大的网络请求工具**，支持 HTTP、HTTPS、FTP 等多种协议。
- **基本用法**：`curl [选项] URL`，常用选项如 `-X`（方法）、`-H`（请求头）、`-d`（POST 数据）。
- **适用场景**：API 测试、文件下载、调试网络请求等。

你可以通过 `man curl` 或 `curl --help` 查看更多选项！ 🚀

## Issue

(base) thyme@chenjingdeMacBook-Pro ~ % curl -X GET https://reqres.in/api/users?page=2
zsh: no matches found: https://reqres.in/api/users?page=2

这个错误是因为 `zsh`（Z Shell）默认会把 `?` 当作 **通配符**（wildcard），而你的 URL 里有 `?page=2`，导致 zsh 尝试匹配文件名而不是直接发送请求。

---

### **解决方法**
#### **1. 用引号包裹 URL（推荐）**
```bash
curl -X GET "https://reqres.in/api/users?page=2"
```
👉 **推荐**：用双引号 `"` 或单引号 `'` 包裹 URL，防止 `?` 被解析。

#### **2. 转义 `?`（加反斜杠）**
```bash
curl -X GET https://reqres.in/api/users\?page=2
```
👉 适用于临时使用，但不如引号方便。

#### **3. 禁用 zsh 的通配符（临时）**
```bash
set -o noglob  # 禁用通配符
curl -X GET https://reqres.in/api/users?page=2
set +o noglob  # 恢复通配符
```
👉 一般不推荐，除非你有特殊需求。


---

### **总结**
- **问题原因**：`zsh` 把 `?` 当作通配符，导致 URL 被解析错误。
- **解决方案**：
  - **✅ 最佳实践**：用引号包裹 URL（`"https://..."`）。
  - **⚠️ 替代方案**：转义 `?`（`\?`）或临时禁用通配符。
- **测试 API**：`reqres.in` 是一个免费的测试 API 服务，适合练习 `curl`。

下次记得加引号哦！🚀
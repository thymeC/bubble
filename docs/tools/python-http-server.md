Python 自带了一个简单的 HTTP 服务器模块，可以用一个命令快速启动一个本地 HTTP 服务器，适合用于文件共享、测试或临时使用。

### **Python 内置 HTTP 服务器**
在终端运行（Python 3）：
```bash
python -m http.server
```
默认端口是 `8000`，访问地址：  
👉 [http://localhost:8000](http://localhost:8000)

#### **可选参数**
- 指定端口（如 `8080`）：
  ```bash
  python -m http.server 8080
  ```
- 允许外部访问（`0.0.0.0`）：
  ```bash
  python -m http.server 8000 --bind 0.0.0.0
  ```
- 后台运行（Linux/macOS）：
  ```bash
  python -m http.server 8000 &
  ```

---

### **其他轻量级 Python HTTP 服务器包**
如果你需要更多功能（如上传文件、目录浏览控制），可以试试这些第三方包：

1. **`http-server`（Node.js，但也很方便）**  
   先安装 Node.js，然后运行：
   ```bash
   npx http-server
   ```

2. **`uploadserver`（Python，支持文件上传）**  
   ```bash
   pip install uploadserver
   python -m uploadserver
   ```

3. **`FastAPI` + `Uvicorn`（适合轻量级 API）**  
   ```bash
   pip install fastapi uvicorn
   echo "from fastapi import FastAPI; app = FastAPI()" > server.py
   uvicorn server:app --reload
   ```

---

### **适用场景**
- ✅ **临时文件共享**（如局域网传文件）
- ✅ **前端开发测试**（HTML/JS/CSS 预览）
- ✅ **快速 API 模拟**（配合 `FastAPI`）
- ❌ **不适合生产环境**（性能和安全有限）

试试看吧！🚀
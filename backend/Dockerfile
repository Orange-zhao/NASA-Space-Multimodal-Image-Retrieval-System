# 基础镜像：用官方 Python 3.11 的精简版本
FROM python:3.11-slim

# 设置工作目录（容器里的"当前文件夹"）
WORKDIR /app

# 先复制依赖文件，安装依赖
# （单独这一步是为了利用 Docker 缓存：
#   只要 requirements.txt 没变，这层就不用重新安装）
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 再复制所有代码
COPY . .

# 容器启动时运行的命令
# uvicorn 是 FastAPI 的服务器
# --host 0.0.0.0 表示允许外部访问（不只是本机）
# --port 8001 是端口号
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8001"]
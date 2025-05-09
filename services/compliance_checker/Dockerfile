# 使用多阶段构建 - 构建阶段
FROM python:3.9-slim as builder

# 设置工作目录
WORKDIR /app

# 安装构建依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# 复制依赖文件
COPY services/compliance_checker/requirements.txt .

# 安装依赖到用户目录
RUN pip install --user --no-cache-dir -r requirements.txt

# 最终镜像阶段
FROM python:3.9-slim

# 设置工作目录
WORKDIR /app

# 复制构建阶段安装的依赖
COPY --from=builder /root/.local /root/.local

# 安装运行时依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# 设置环境变量
ENV PATH=/root/.local/bin:$PATH
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV PYTHONPATH=/app
ENV PORT=8003

# 复制应用代码
COPY services/compliance_checker /app/

# 确保入口脚本有执行权限
RUN chmod +x /app/entrypoint.sh

# 暴露端口
EXPOSE 8003

# 健康检查
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8003/health || exit 1

# 启动命令
ENTRYPOINT ["/app/entrypoint.sh"] 
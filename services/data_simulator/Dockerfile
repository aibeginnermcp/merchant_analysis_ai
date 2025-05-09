# 多阶段构建 - 构建阶段
FROM python:3.9-slim AS builder

WORKDIR /app

# 安装构建依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# 复制依赖文件
COPY services/data_simulator/requirements.txt .

# 安装依赖到用户目录
RUN pip install --user --no-cache-dir -r requirements.txt

# 最终镜像
FROM python:3.9-slim

WORKDIR /app

# 复制已安装的依赖
COPY --from=builder /root/.local /root/.local

# 安装运行时依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# 设置环境变量
ENV PATH=/root/.local/bin:$PATH
ENV PYTHONPATH=/app
ENV PORT=8000

# 复制应用代码
COPY services/data_simulator /app/

# 确保入口脚本有执行权限
RUN chmod +x /app/entrypoint.sh

# 暴露端口
EXPOSE 8000

# 健康检查
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# 启动命令
ENTRYPOINT ["/app/entrypoint.sh"] 
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir --default-timeout=600 --retries=10 streamlit pandas matplotlib statsmodels pyarrow airportsdata anthropic
RUN pip install --no-cache-dir --default-timeout=600 --retries=10 chromadb

COPY . .

EXPOSE 8501

ENTRYPOINT ["streamlit", "run", "app.py", "--server.port=8501", "--server.address=0.0.0.0"]
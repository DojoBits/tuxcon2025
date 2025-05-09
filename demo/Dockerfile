# Base image
FROM python:3.12-slim-bullseye

# Set working directory
WORKDIR /app

# Prevents Python from writing pyc files and buffering stdout/stderr
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Copy requirements first for better caching
COPY requirements.txt .

# Install dependencies
RUN pip install --upgrade pip \
    && pip install -r requirements.txt

# Copy the rest of the application code
COPY myproject .

# Use JSON array format for CMD to ensure signal handling works properly
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]


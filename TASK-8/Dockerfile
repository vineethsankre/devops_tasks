# Base image
FROM python:3.11-slim

# Working directory
WORKDIR /app

# Copy application files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose required port
EXPOSE 8080

# Run the application
CMD ["python", "app.py"]


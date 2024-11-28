FROM python:3.11

# Set the working directory to /app 
WORKDIR /app 

# Update package list and install neccesary build dependencies 
RUN apt-get update -y && apt-get install -y --no-install-recommends \
    curl \
    bash \
    && rm -rf /var/lib/apt/lists/*

# Install Poetry
RUN curl -sSL https://install.python-poetry.org | python3 -

# Add poetry to PATH 
ENV PATH="/root/.local/bin:$PATH"

# Copy poetry config files to take advantage of Docker Cache
COPY pyproject.toml poetry.lock ./
    
# Copy the rest of the source code into the container 
COPY . . 

# Install dependencies 
RUN poetry install 

# Make the prestart.sh script executable 
RUN chmod +x ./prestart.sh

# Expose the port the application runs on 
EXPOSE 8000

# Command to run the executable and start the server
CMD ["sh", "-c", "poetry run bash ./prestart.sh && poetry run uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload"]
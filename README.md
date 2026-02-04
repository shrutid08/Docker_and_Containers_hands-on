#Hands-on L3: Containers with Docker

#Objective

This hands-on exercise demonstrates how to install Docker Desktop and deploy a multi-container microservice application using Docker and Docker Compose. The application consists of a Python Flask web service, a Redis cache, and a PostgreSQL database running in a Docker container.

#Tools and Technologies
```
- Docker Desktop
- Docker Compose
- Python (Flask)
- Redis
- PostgreSQL
- GitHub
```

#Project Structure
```
.
├── app.py
├── Dockerfile
├── compose.yaml
├── requirements.txt
├── README.md
```

##Docker Installation and Verification

Docker Desktop was installed based on the operating system.

#PostgreSQL Setup Using Docker

Pull the PostgreSQL image:

```docker pull postgres```


Run the PostgreSQL container:

```docker run -d -p 5432:5432 --name postgres1 -e POSTGRES_PASSWORD=pass12345 postgres```


Connect to PostgreSQL using psql:

```psql -d postgres -U postgres```

##Flask and Redis Application
#requirements.txt
```
flask
redis
```

#app.py
```
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError:
            if retries == 0:
                raise
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return f'Hello World! I have been seen {count} times.\n'
 ```


#Dockerfile
```
FROM python:3.7-alpine

WORKDIR /code

ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0

RUN apk add --no-cache gcc musl-dev linux-headers

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

EXPOSE 5000

COPY . .

CMD ["flask", "run"]
```

#Docker Compose Configuration
```
version: "3.9"

services:
  web:
    build: .
    ports:
      - "8000:5000"
    depends_on:
      - redis

  redis:
    image: redis:alpine
```



#Running the Application

Build and start the containers:

```docker compose up ```

Open the application in a browser:

http://localhost:8000

Expected output:

```Hello World! I have been seen X times.```

#Errors Faced and Issues Logged

During the initial execution, the application failed to start due to intentionally introduced errors in the starter code. These included:

Incorrect Flask import statements

Application not accessible on localhost

All errors were documented and tracked using the GitHub Issues feature, along with resolution comments after fixes were applied.

##Screenshots

Containers on Docker Desktop:
![Docker Desktop](https://github.com/user-attachments/assets/065c3ba7-7ed4-43bb-8eea-45d7be738deb)

Expected Output:
![Output](https://github.com/user-attachments/assets/bcf0938d-157d-438c-a701-63cba0a31b30)



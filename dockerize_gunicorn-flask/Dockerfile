FROM python:latest
RUN apt update
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
RUN pip install gunicorn
EXPOSE 8000
ENTRYPOINT python3 -m gunicorn -w 4 application:app -b 0.0.0.0:8000
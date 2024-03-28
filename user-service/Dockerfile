# syntax=docker/dockerfile:1

FROM python:3.8-slim-buster
WORKDIR /python-docker

COPY ..
RUN pip install -r requirements.txt

COPY . .

CMD [ "python", "app.py"]
EXPOSE 3001
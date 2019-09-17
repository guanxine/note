FROM python:2.7-slim
# 工作目录切换到 app
WORKDIR /app

ADD . /app
# run shell
RUN pip install --trusted-host pypi.python.org -r requirements.txt

EXPOSE 80

ENV NAME WORLD

# docker run python app.py
CMD ["python", "app.py"]
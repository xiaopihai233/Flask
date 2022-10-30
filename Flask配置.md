# Flask配置



```
uname -r
```

3.10以上

![image-20221031005123235](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031005123235.png)



yum update -y
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine



yum install  yum-utils -y

yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
	 yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum -y install docker-ce docker-ce-cli containerd.io

![image-20221031000745054](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031000745054.png)



docker version

![image-20221031000817043](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031000817043.png)



mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
"registry-mirrors": ["https://dgxy4fzg.mirror.aliyuncs.com"]
}
EOF

![image-20221031000411486](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031000411486.png)



systemctl daemon-reload
systemctl restart docker
systemctl enable docker

![image-20221031001517295](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031001517295.png)



curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

![image-20221031000355673](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031000355673.png)



 chmod +x /usr/local/bin/docker-compose

 ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

docker-compose --version

![image-20221031002714830](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031002714830.png)



#安装python3
whereis python
cd /usr/bin/
ll python*

![image-20221031000909825](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031000909825.png)



yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make  wget -y

![image-20221031001122817](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031001122817.png)



wget https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tar.xz

![image-20221031001212396](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031001212396.png)



xz -d Python-3.9.6.tar.xz
tar -xf Python-3.9.6.tar
cd Python-3.9.6

![image-20221031003217544](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031003217544.png)



./configure prefix=/usr/local/python3

![image-20221031003239329](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031003239329.png)



make && make install

![image-20221031003458822](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031003458822.png)



yum install zlib zlib-devel -y
mv /usr/bin/python /usr/bin/python.bak
ln -s /usr/local/python3/bin/python3.9 /usr/bin/python3
python3 -V

![image-20221031003545389](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031003545389.png)



vi /usr/bin/yum
vi /usr/libexec/urlgrabber-ext-down
#! /usr/bin/python2
python 2/3

![image-20221031003648187](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031003648187.png)



#安装python-pip
yum -y install epel-release -y

yum -y install python-pip -y

yum -y install python3-pip -y

pip3 install --upgrade pip

![image-20221031004648162](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031004648162.png)



pip3 install docker-compose

![image-20221031005158056](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031005158056.png)



docker-compose version

![image-20221031005520337](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031005520337.png)



yum install git -y
git clone https://hub.yzuu.cf/docker/awesome-compose.git

![image-20221031005618859](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031005618859.png)

![image-20221031005711743](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031005711743.png)



mkdir flask-ljx
vim app.py

from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! 该页面已被访问 {} 次。\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)

![image-20221031010122481](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010122481.png)



vim Dockerfile

FROM python:3.6-alpine
ADD . /code
WORKDIR /code
RUN pip3 install redis flask
CMD ["python", "app.py"]

![image-20221031010051127](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010051127.png)




vim docker-compose.yml

version: '3'
services:

  web:
    build: .
    ports:

​	- "5000:5000"

  redis:
    image: "redis:alpine"

![image-20221031010013808](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010013808.png)



后台启动

docker-compose up -d

![image-20221031010355445](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010355445.png)

![image-20221031010603077](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010603077.png)



curl localhost:5000

![image-20221031010707295](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010707295.png)



停止

docker-compose down

![image-20221031010725260](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010725260.png)



查询容器列表

docker-compose ps

![image-20221031010817017](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010817017.png)



查询日志 (查询所有日志，可以辅助排查个别容器启动失败问题)

docker-compose logs

![image-20221031010834813](C:\Users\sakura\AppData\Roaming\Typora\typora-user-images\image-20221031010834813.png)
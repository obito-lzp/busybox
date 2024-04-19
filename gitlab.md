# 系统环境
```
[win]notepad C:\Windows\System32\drivers\etc\hosts
[linux]vim /etc/hosts
10.83.2.183	xgitlab.com

[win]绕过代理
xgitlab.com;
```
# 安装gitlab
```
docker pull gitlab/gitlab-ce:14.6.1-ce.0
mkdir -p /root/05.gitlab  #/root/05.gitlab可以修改成合适的目录

docker run --detach \
  --hostname xgitlab.com \
  --publish 443:443 --publish 80:80 --publish 23:22 \
  --name gitlab \
  --restart always \
  --volume /root/05.gitlab/config:/etc/gitlab \
  --volume /root/05.gitlab/logs:/var/log/gitlab \
  --volume /root/05.gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:14.6.1-ce.0
  
docker logs -f gitlab #查看安装过程中的日志
docker exec -it gitlab /bin/bash
gitlab-rails console #这一步比较慢，大概在1分钟左右，请耐心等待
user=User.find_by_username 'root'
user.password="88888888"   #这里请换成一个复杂的密码
user.save!
quit
exit
```
# 登录
xgitlab.com
## 设置中文
头像->偏好设置->本地化->语言
## 添加SSH密钥
ssh-keygen -t rsa -C "liuzhengpei@xfusion.com"  
~/.ssh/id_rsa.pub
## 安装gitlab-runner
```
docker pull gitlab/gitlab-runner:v14.6.1
mkdir -p /root/06.gitlab-runner  #/root/06.gitlab-runner可以修改成合适的目录

docker run -itd \
  --name gitlab-runner \
  --restart always \
  -v /root/06.gitlab-runner/config:/etc/gitlab-runner \
  -v /root/02.pkg/:/root/pkg \
  gitlab/gitlab-runner:v14.6.1 
  
docker exec -it gitlab-runner bash
echo "172.19.50.2	xgitlab.com" >> /etc/hosts

gitlab-runner register \
  --non-interactive \
  --executor "shell" \
  --url "http://xgitlab.com/" \
  --registration-token "ysdJ8d7otiVpnqseXvFV" \
  --description "xbook-builder" \
  --tag-list "build" \
  --locked="false" \
  --access-level="not_protected"
  
```

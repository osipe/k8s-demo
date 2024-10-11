# Minikube Example
## Mô Tả
Đây là các cấu hình cho việc cài đặt hệ thống K8s lb đơn giản với Minikube
## Yêu Cầu
- Cài đặt docker, Minikube, Nginx
- Cài đặt WSL nếu đang dùng Window
## Cấu hình hệ thống
###Tạo user K8s
- Tạo user K8s
```bash
sudo adduser k8s
```
- Cấp người dùng k8s có quyền sudo
```bash
sudo usermod -aG sudo k8s
```
- Chuyển sang người dùng k8s
```bash
su - k8s
```
###Cài đặt docker
- Cập nhật hệ thống
```bash
sudo apt update
```
- Cài đặt các gói cần thiết
```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
```
- Cấp quyền cho người dùng
```bash
sudo usermod -aG docker k8s
```
- Cấu hình docker
```bash
sudo vi /etc/docker/daemon.json
{
  "dns": ["8.8.8.8", "8.8.4.4"],
  "iptables": false
}
```
- Khởi động Docker
```bash
sudo service docker start
```
- Kiểm tra
```bash
docker info
```
###Cài đặt  Minikube
- Cài đặt
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```
- Khởi động
```bash
minikube start
```
- Áp dụng alias
```bash
echo 'alias kubectl="minikube kubectl --"' >> ~/.bashrc
source ~/.bashrc
```
- Show dashboard K8s
```bash
minikube dashboard &
```
Nếu không hoạt động kiểm tra lại iptables Version => chuyển sang dùng /usr/sbin/iptables-legacy
```bash
sudo update-alternatives --config iptables
```
- Kích Hoạt Ingress
```bash
minikube addons enable ingress
```
- Tạo thư mục example copy nội dung thư mục k8s vào thư mục Example -> apply
```bash
 kubectl apply -f deployment.yaml
 kubectl apply -f service.yaml
 kubectl apply -f ingress.yaml
```
- Bổ sung IP Minikube vào tệp hosts
```bash
minikube ip
sudo vi /etc/hosts
192.168.49.2 example-lb.com
```
- Kiểm tra
```bash
curl http://example-lb.com
```
###Expose app với Nginx
- Cài đặt
```bash
sudo apt install nginx
```
- Cấu hình: copy thư mục nginx.conf vào thư mục /etc/nginx/conf.d/, Kiểm tra
```bash
sudo nginx -t
```
- Khởi động
```bash
sudo service nginx restart
```
- Kiểm tra lấy IP WSL -> mở browser truy cập <IP>:81
```bash
ip a
```



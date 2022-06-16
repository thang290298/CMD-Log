<h1 align="center">Tài liệu xây dựng hệ thống ELK Stack </h1>

# Phần I. Tổng quan
## 1. Mô hình hệ thống
## 2. Thành phần chức năng

| <p align="center">Node</p> | <p align="center">Host name</p> | <p align="center">role</p> | <p align="center">Cấu hình</p> | <p align="center">Vai trò</p> |
|------|-----------|------|----------|-----------|
| Master | ELK-Master | <ul><li>master</li><li>voting_only</li></ul> | <ul><li></li></ul> | <ul><li>Có trách nhiệm quản lý, tạo, sửa, xóa index, tái sắp xếp shard, thêm hoặc xóa 1 node ra khỏi cụm cluster</li><li>Chỉ định nút có đủ điều kiệu lên làm master trong cụm cluster đã được khai báo </li></ul> |
| Data1 | ELK-Data1 | <ul><li>data_hot</li><li>data_content<li>master</li></ul> | <ul><li></li></ul> | <ul><li>Sử dụng để lưu trữ dữ liệu vửa được đẩy vào từ logstash và có lượng tìm kiếm lớn</li><li>Sử dụng backup dự phòng cho node master </li></ul> ||  |
| Data2 | ELK-Data2 | <ul><li>data_warm</li><li>data_cold</li><li>data_frozen<li>master</li></ul>| <ul><li> </li></ul> | <ul><li>Lưu trữ dữ liệu ít được tìm kiếm</li><li>Sử dụng backup dự phòng cho node master </li></ul> |
| Ingest | ELK-Ingest | <ul><li>Ingest</li></ul> | <ul><li></li></ul> | <ul><li> Xử lý dữ documents trước khi quán trình index bắt đầu  </li></ul> |
| Coordinating | ELK-Coordinating | <ul><li>[ ]</li></ul> | <ul><li></li></ul> | <ul><li>Có trach nhiệm điều phối, xử lý các request</li></ul> |
| Transform | ELK-Transform  | <ul><li>transform</li><li>remote_cluster_client</li></ul> | <ul><li></li></ul> | <ul><li>thực hiện chuyển đổi các chỉ số Elasticsearch và các chỉ số API </li></ul> |
| Machine-learning | ELK-Machine-learning | <ul><li>ml</li><li>remote_cluster_client</li></ul>| <ul><li></li></ul> | <ul><li>Xử lý các tiến trình API requests</li></ul>|



# Phần II. Triển khai cài đặt
## 1. Cài đặt chug trên tất cả các node Elasticsearch
### Thực hiện cài đặt Docker Compose
- Thực hiện update OS:
```sh
sudo apt-get update -y
```
- Cài đặt các gói ràng buộc
```sh
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
```
- Adding Docker’s GPG Key

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

- Cài đặt Docker
```sh
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu  $(lsb_release -cs)  stable"
sudo apt update
sudo apt-get install docker-ce -y
```
- Kiểm tra version docker cài đặt:
```sh
docker --version
kết quả:
Docker version 20.10.16, build aa7e414
```
- Cài đặt Docker Compose
```sh
apt-get install docker-compose -y
```
- kiểm tra version docker compose sẽ thực hiện cài đặt
```sh
$ apt-cache madison docker-compose-plugin
docker-compose-plugin | 2.5.0~ubuntu-focal | https://download.docker.com/linux/ubuntu focal/stable amd64 Packages
docker-compose-plugin | 2.3.3~ubuntu-focal | https://download.docker.com/linux/ubuntu focal/stable amd64 Packages
```
- cài đặt docker compose version 2.5.0
```sh
sudo apt-get install docker-compose-plugin=2.5.0~ubuntu-focal
```
- Kiểm tra phiên bản docker compose sau khi cài đặt
```sh
$  docker compose version
Docker Compose version v2.5.0
```
### Điểu chỉnh bộ nhớ ảo
Điều chỉnh số lượng mmap để tránh trường hợp hết bộ nhớ ảo
- cập nhật thông số trong file: `/etc/sysctl.conf`
```sh
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
```
- kiểm tra lại:
```sh
sysctl -p
```

### Tăng giới hạn mô tả file đang mở
- Thay đổi giá trị trong file `/etc/security/limits.conf`
```sh
echo "- nofile 65536" >> /etc/security/limits.conf
```
### Cập nhật thông tin các node trong file host
```sh
echo "# list node ELK
192.168.70.51 elk-master
192.168.70.52 elk-data1
192.168.70.53 elk-data2
192.168.70.54 elk-data3
192.168.70.55 elk-ingest
192.168.70.56 elk-cordinating
192.168.70.57 elk-transform
192.168.70.58 elk-machine-learning
192.168.70.59 logstash
192.168.70.60 kibana
192.168.70.61 elk-remote
" >> /etc/hosts
```

### Mở Port firewalld
```sh
sudo ufw allow 9200
sudo ufw allow 9300
```
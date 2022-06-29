<h1 align="center">Tổng hợp vê lưu trữu data trên Elasticsearch</h1>

# Phần I. Path data
## 1. Data Path
- Elasticsearch lưu trữ dữ liệu data nhận về và thông tin log hoạt động thông qua những đường dẫn lưu trữ data cụ thể.
- Trên từng HDH cài đặt ứng dụng thì hệ thống sẽ có những đường dẫn lưu trữ riêng tùy theo hệ điều hành cụ thể
- Để có thể đảm bảo về dung lượng lưu trữ và tính an toàn dữ liệu có thể thực hiện thay đổi đường dẫn lưu trữ theo ý muốn.

- Định dạng cấu hình trong files `elasticsearch.yml`:
```sh
path.data: /var/data/elasticsearch
path.logs: /var/log/elasticsearch
```
## 2. Cơ chế lưu trữ data trong cụm Cluster
- Trong một cụm Cluster khi dữ liệu được đẩy về từ Logstash sẽ được thông báo đến node master, từ đây node master thực hiên kiểm tra, phân công nhiệm vụ xử lý dữ liệu chuyển về và quyết định dữ liệu logs đó sẽ được lưu trữ tại đâu tùy theo cấu hình hiền thết lập về số lượng primary shard và replicas shard được áp dụng cho index 
- Hệ thống đảm bảo các primary shard và replicas shard của một index sẽ được lưu trữ trên nhiều server khác nhau và cùng ghi dữ liệu đồng thời trên các server
- Khi 1 node trong cụm cluster đạt một múc sử dụng dung lượng disk nhất định không thể ghi thêm dữ liệu vào node, hệ thống sẽ thực hiện dừ ghi dữ liệu trên node đó và đẩy dữ liệu vào 1 node khác. Để thực hiện điều này cần thực hiện cấu hình mức độ cảnh bảo sử dụng trên các node để node đó có thể thoog báo đến master node tình trạng thái lưu trữ khi có yêu cầu lưu trư data
- Tương tụ đối vói 1 node 
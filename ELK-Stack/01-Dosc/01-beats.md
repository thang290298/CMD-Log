<h1 align="center">Ghi chép về Beats</h1>
## Mục lục
[Lời nói đầu](#loinoidau)

I.  [Beats là gì ?](#beats?)

II. [Khái niệm về ELK Stack](#ELKstack)

III. [Thành Phần](#thanhphan)

IV. [Các trường hợp sử dụng ELK Stack](#sudung)

V. [Vì sao nên sử dụng ELK Stack](#visao)

<h3 align="center">-----------------------------------------</h3>


## Phần I. <a name="beats?"></a>Beats là gì ?
- Beats là những data shipper mã nguồn mở được thực hiện cài đặt giống như các Agent trên các server để thu thập dữ liệu về các sự kiện và gửi các dữ liệu đó về Elasticsearch. Beats có thể gửi dữ liệu trực tiếp tới Elasticsearch hay tới Logstash.
- Beats là một platform trong đó có các project nhỏ sinh ra thực hiện trên từng loại dữ liệu nhất định.
- Các beat index pattern cần được cài đặt trên cả ELK server và các client. Trên ELK server, các beat sẽ kết hợp với các thành phần để lọc dữ liệu, đánh chỉ mục, hiển thị.
- Một số beats Partern thường được dùng là :
  - Packetbeat : Thực hiện gửi dữ liệu được capture từ các port về server.
  - Topbeat : Như là một monitor agent, giúp chúng ta có thể thu thập các thông tin về phần cứng như là : CPU, RAM,…


  
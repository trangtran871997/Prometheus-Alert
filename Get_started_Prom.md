## 1. Prometheus

### 1.1. Khái niệm

- Là một trong số các công cụ hữu ích để giám sát server.

- Dữ liệu mà Promt lưu trữ có thể là các metric (thông số) về tình trạng server như:

+ RAM, CPU đã dùng của mỗi service

+ Số lượng requests tới server

+ Dung lượng đĩa, ...

- Các metric luôn gắn với một mốc thời gian, tạo thành chuỗi dữ liệu theo thời gian

=> Thông qua Prometheus có thể xem dữ liệu của server vào khoảng thời gian trước đó để theo dõi giám sát các server, khi có sự cố xảy ra kịp thời xử lý.

### 1.2. Cách hoạt động

- Prometheus pull các metric về qua HTTP trong khoảng thời gian do ta thiết lập.

- Các service không thể tự export được các metric do đó cần đến Instructmentation/Exporter.

+ Exporter : Là chương trình giúp thu thập, chuyển đổi các metric không ở dạng kiểu dữ liệu chuẩn Prometheus sang chuẩn dữ liệu Prometheus. Prometheus lấy thông tin: RAM, CPU, Disk, Network, ... tổng hợp và xuất ra kênh truy cập các metric ở port TCP 9100 để Prometheus đi lấy dữ liệu metric cho việc giám sát.

+ Instructmentation: là những client-libraries được cung cấp bởi Prometheus hoặc một bên thứ 3 nào đó, để cài vào ứng dụng, giúp tùy biến những metrics riêng của hệ thống. VD: số lượng người login vào website


- Prometheus có một built-in exporter, export các metrics về service prometheus ra tại URI: http://prometheus.lc:9090/metrics.

### 1.3. Cài đặt Prometheus

- Sử dụng docker để cài đặt Prometheus tự động scrape dữ liệu từ chính nó

#### B1: Scrape metrics từ built-in exporter

- File cấu hình đề prometheus scrapes metrics từ built-in exporter:  prometheus sẽ có một target để pull metrics về có tên là prometheus, tự động scrape mỗi 30s tới service đang chạy ở localhost:9090.
```
vim scrape_metric.yml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s

scrape_configs:
  - job_name: prometheus
    scrape_interval: 30s
    static_configs:
      - targets:
        - localhost:9090

  - job_name: cadvisor
    scrape_interval: 10s
    static_configs:
      - targets:
        - cadvisor:8080
```

#### B2: Cấu hình để chạy prometheus lên với docker:

`vim docker-compose.yml`

```
version: '3.6'

volumes:
  grafana-data:
  prometheus-data:

services:
  prometheus:
    image: prom/prometheus:v2.12.0
    command:
      - --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - prometheus-data:/prometheus
```

### 1.4. Prometheus + cAdvisor

- Để collect thêm các thông số của các service khác trên máy => scrape thêm metrics từ cAdvisor.

#### B3: Add cAdvisor vào docker-compose:
```
version: '3.6'

volumes:
  grafana-data:
  prometheus-data:

services:
  prometheus:
    image: prom/prometheus:v2.12.0
    command:
      - --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - prometheus-data:/prometheus
    links:
      - cadvisor:cadvisor
      - alertmanager:alertmanager
    depends_on:
      - cadvisor
    restart: always
    deploy:
      mode: global


  node-exporter:
    image: prom/node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - --collector.filesystem.ignored-mount-points
    ports:
      - 9100:9100
    restart: always
    deploy:
      mode: global

  cadvisor:
    image: google/cadvisor:latest
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    ports:
      - 8008:8080

  grafana:
    image: grafana/grafana:6.3.5
    ports:
      - 3000:3000
    environment:
      GF_SECURITY_ADMIN_PASSWORD: secret
    volumes:
      - grafana-data:/var/lib/grafana
```

=> Truy cập http://localhost:9090 để vào dashboard của Prometheus

=> Truy cập http://localhost:3000 để vào dashboard của Grafana với username là admin và password là secret


## 2. Alert rule

- Ngoài khả năng lưu trữ, cung cấp một bộ alert mạnh mẽ, giúp gửi thông báo tới các kênh khi các metrics đạt giới hạn. VD: qua Email.

- Về cơ bản, bộ Alerting của Prometheus được tách thành hai thành phần:

- Alerting Rules chạy cùng service

- Alert Manager được tách thành một service độc lập.

- Alerting Rules sẽ gửi alerts tới Alert Manager. Alert Manager sẽ chịu trách nhiệm quản lý, tổng hợp các alerts và thực hiện gửi chúng tới nơi cần đến.

- Alert Manager: Để tránh gửi các thông báo trùng lặp do metrics thay đổi liên tục, chặn các alert ngay sau khi đã có một alert được gửi đi thành công, nếu không Email sẽ bị Flood. Ngoài ra AM có thể gom nhóm các Alert lại rồi mới gửi đi.

### 2.1 Alerting Rules

- Cho phép xác định các điều kiện để đưa ra cảnh báo

```
ALERT <alert name>
  IF <expression>
  [ FOR <duration> ]
  [ LABELS <label set> ]
  [ ANNOTATIONS <label set> ]
```

**VD:**
```
- alert: high_load
    expr: node_load1 > 0.8
    for: 30s
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} under high load"
      description: "{{ $labels.instance }} of job {{ $labels.job }} is under high load."
```

FOR: chờ 1 khoảng thời gian để đưa ra cảnh báo

LABELS: đặt nhãn cho cảnh báo

ANNOTATIONS: Chứa thêm các thông tin cho cảnh báo


=> Theo VD trên: Cảnh báo high mem load, nếu lướn hơn 80% -> chờ 30s để bắn cảnh báo, 2 dòng sau bổ sung thêm thông tin về instance đó.


- Alert có 2 chế độ (Pending và firing), nếu hoạt động sẽ có value là 1, ko hoạt động sẽ có value là 0


### 2.2. Alertmanager

- Đưa ra các thông báo: Silence, Inhibiton, Grouping, ... và gửi thông báo qua mail.

- Grouping : Phân loại cảnh báo theo group. Gom chung vào 1 group thì sẽ nhận được 1 notification chứa đầy đủ thông báo của thành phần trong group.

- Inhibiton: Sẽ bỏ đi các cảnh báo nhất định nếu một số cảnh báo khác đã được bắn.

- Nếu trên server đặt các alert về network, web-server, mysql, ... Khi mất kết nối Internet cảnh báo network sẽ được gửi về cho admin, sử dụng Inhibiton để không cần nhận cảnh báo từ  web-server, mysql, ... nữa.

```
inhibit_rules:
- source_match:
    severity: 'critical'
  target_match:
    severity: 'warning'
  # Apply inhibition if the alertname is the same.
  equal: ['alertname', 'cluster', 'service']
  
 ```
=> Nếu critical đã được gửi đi thì thông báo warning không gửi đi nữa, áp dụng với các thông báo có cùng các label là: alertname, cluster và service.

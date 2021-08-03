## Tìm hiểu các khái niệm liên quan đến HBA (Host Bus Adapters)

- Trong hệ thống SAN có 3 thành phần chính:

- Thiết bị lưu trữ: là các tủ đĩa có dung lương lớn, khả năng truy xuất nhanh, có hỗ trợ các chức năng RAID, local Replica,...tủ đĩa này là nơi chứa dữ liệu chung cho toàn bộ hệ thống.

- Thiết bị chuyển mạch SAN: đó là các SAN switch thực hiện việc kết nối các máy chủ đến tủ đĩa.

- Các máy chủ hoặc máy trạm cần lưu trữ, được kết nối đến SAN switch bằng cáp quang thông qua HBA card.

- HBA là thiết bị quan trọng cho hoạt động đúng của SAN. Các vấn đề phát sinh liên quan đến HBA bao gồm từ cấu hình không phù hợp đến trình điều khiển thiết bị hoặc BIOS lỗi thời.

- Sau khi HBA được cài đặt trên máy chủ và máy chủ được bật nguồn, cần có công cụ giám sát và quản lý :

```

Vendor            Management Software

QLogic                 Sansurfer

Emulex              OneCommand Manager
```

### I. Tìm hiểu về Fibre Chanel và Ethernet

#### a. Fibre Chanel

- Được thiết kế cho mạng vùng lưu trữ (SAN), là công nghệ mạng tốc độ cao được sử dụng để kết nối lưu trữ dữ liệu máy tính với máy chủ, cung cấp giao diện điểm-điểm, chuyển đổi và vòng lặp để cung cấp theo thứ tự và không mất dữ liệu khối.

- Có hiệu suất đáng tin cậy, xác định với độ trễ thấp và là giao thức truyền dữ liệu tốc độ cao

- Loại giao thức :
```

Fibre Channel Protocol (FCP): là giao thức vận chuyển các lệnh SCSI qua Fibre Chanel Network.

FICON là một giao thức vận chuyển các lệnh ESCON qua Fibre Chanel

Giao thức NVMe: Fibre Chanel được sử dụng để vận chuyển dữ liệu từ các hệ thống lưu trữ sử dụng phương tiện lưu trữ bộ nhớ flash

```

- Trong cấu trúc liên kết cấu trúc chuyển mạch, tất cả các thiết bị được kết nối và liên lạc qua các switch.

- Fibre Chanel switch, cụ thể là một thiết bị kết nối tương thích với Giao thức Kênh sợi quang (FCP) và có các tính năng với hiệu suất cao, độ trễ thấp và truyền dẫn không mất dữ liệu.

- Được biết đến như một trong những thành phần chính được sử dụng trong SAN, Fibre Chanel switch đóng vai trò quan trọng trong việc kết nối nhiều cổng lưu trữ và máy chủ.


#### b. Ethernet

- Ethernet là giao thức 802.3.

- Ethernet đề cập đến công nghệ kết nối máy tính và các thiết bị khác thông qua một giao thức, được sử dụng phổ biến nhất trong các mạng cục bộ có dây (LAN).

- Khi triển khai mạng Ethernet, không thể thiếu các thiết bị chuyển mạch Ethernet (Ethernet switch). Bộ chuyển mạch Ethernet là các khối xây dựng cơ bản của các mạng, kết nối các thiết bị Ethernet với nhau.


#### c. Sự khác nhau của Fibre Chanel và Ethernet

- Xét dựa trên 3 tiêu chí : độ tin cậy, tốc độ truyền và chi phí.

1. Độ tin cậy

- Fibre Chanel được coi là một giao thức lossless. Các bộ chuyển mạch kênh không mất dữ liệu, tất cả các khung dữ liệu được truyền theo thứ tự. Trong khi các bộ chuyển mạch Ethernet có thể có nguy cơ bị rớt khung, khi chỉ dựa vào layer bên trên và giao thức TCP để đảm bảo hoạt động.


2. Tốc độ truyền dữ liệu

- Tốc độ dữ liệu tối đa của chuyển đổi Kênh sợi quang ngay từ đầu là 1Gbps. Hiện đã phát triển lên tới 128GFC, với các phiên bản 8GFC, 16GFC và 32GFC có sẵn.

- Tốc độ truyền chuyển mạch Ethernet dao động từ Fast Ethernet, Gigabit Ethernet, 10 Gigabit Ethernet đến 40 / 100GbE.

3. Giá cả

- Các bộ chuyển mạch Ethernet rẻ hơn nhiều so với các bộ chuyển mạch Kênh.

- Tuy nhiên, Kênh sợi chủ yếu được sử dụng trong môi trường lưu trữ trung tâm dữ liệu, trong khi Ethernet có thể được tìm thấy trong các loại mạng khác nhau: từ nhà nhỏ, văn phòng lớn đến trung tâm dữ liệu quy mô lớn.

- Đối với việc sử dụng một trung tâm dữ liệu, 8Gbps FC thường rẻ hơn Ethernet 10Gbps. Và 16GFC có giá tương đương 10GbE.

- Bên cạnh đó, phí bảo trì cũng là một yếu tố cần được xem xét. Trong các hệ thống CNTT lớn, nếu một bộ chuyển mạch Ethernet bị hỏng, hầu hết các quản trị viên có thể xử lý nó. Tuy nhiên, khi có sự cố xảy ra với các Fibre Chanel switch, thay vào đó cần tính đến nhà sản xuất.


### II. Tìm hiểu về QLogic và Emulex

- Thuật ngữ bộ điều hợp bus chủ (HBA) thường được sử dụng để chỉ thẻ giao diện Kênh sợi quang. Nó cho phép các thiết bị trong mạng vùng lưu trữ Fibre Chanel truyền dữ liệu với nhau và kết nối máy chủ với thiết bị chuyển mạch hoặc thiết bị lưu trữ, kết nối nhiều hệ thống lưu trữ hoặc kết nối nhiều máy chủ.

- Mỗi Fibre Chanel HBA có một World Wide Name (WWN) duy nhất, tương tự như địa chỉ MAC Ethernet

- Những thiết bị có cùng tốc độ 8 Gbps được thêm vào những SAN (Storage Area Network – khu vực lưu trữ trong mạng kết nối với nhau dựa trên công nghệ SCSI bằng các đường cáp quang) có sẵn, sẽ giúp cho những đường kết nối trong mạng hoạt động ở tốc độ cao nhất có thể.

- Có hai loại WWN trên HBA; một nút WWN (WWNN), được chia sẻ bởi tất cả các cổng trên bộ điều hợp bus chủ và một WWN cổng (WWPN), là duy nhất cho mỗi cổng. Có các mô hình HBA có tốc độ khác nhau: 1Gbit / s, 2Gbit / s, 4Gbit / s, 8Gbit / s, 10Gbit / s, 16Gbit / s, 20Gbit / s và 32Gbit / s.

- Emulex và Qlogic là 2 nhà máy hứa hẹn sẽ có những sản phẩm 8 Gbps đầu tiên. Công nghệ được đưa ra thị trường sẽ có định dạng giống như 4 Gbps – như là switche, host bus adaters (HBAs – card kết nối một máy tính với một SAN),…

- Công nghệ 8 Gbps mới sẽ giúp cho việc kết nối các switch này đơn giản hơn và có giá thành rẻ hơn, cũng như mở rộng tốc độ cao hơn tới các máy chủ.


#### a. Qlogic 8Gb

- QLogic được tạo ra vào năm 1992 sau khi được Emulex loại bỏ. Kinh doanh ban đầu của QLogic là bộ điều khiển đĩa.

- QLogic đơn giản hóa quá trình lưu trữ mạng, sản xuất chip điều khiển, bộ điều hợp bus chủ (HBA) và bộ chuyển mạch.

- Qlogic phục vụ khách hàng với các giải pháp dựa trên tất cả các công nghệ mạng lưu trữ bao gồm SCSI, Internet SCSI (iSCSI) và Fibre Chanel.


**Ưu điểm của Qlogic:**

- Cấu hình 1, 2 và 4 cổng để linh hoạt tối đa khi thực hiện

- Cho phép xử lý sự cố nhanh chóng và mạnh mẽ

- HBA hỗ trợ chức năng nâng cao với giải pháp phần mềm

- Bộ sản phẩm QLogic làm cho quá trình lưu trữ mạng dễ dàng.

- Sản phẩm cao cấp với mức giá ok.

- Dựa trên các phản hồi từ khách hàng.

- Đảm bảo khả năng tương tác và dễ tích hợp trong các triển khai lưu trữ mạng hiện có.

- Cải thiện bảo mật với khả năng Tải xuống Firmware an toàn.


**Các loại Q Logic:**

- QLogic 2700 được sử dụng cho cả triển khai chế độ máy chủ và chế độ đích với các tùy chọn. Cung cấp tối đa Fibre Chanel để cho phép các giải pháp flash và SSD, ảo hóa siêu quy mô và kiến trúc trung tâm dữ liệu mới.

- QLogic 2600 được cải tiến Bộ điều khiển Fibre Chanel HBA Gen 5 (16Gb), mang lại hiệu suất tối ưu cho Kênh, sử dụng năng lượng, độ tin cậy.

- QLogic 8300 cung cấp tính toàn vẹn dữ liệu đầu cuối, lý tưởng cho các ứng dụng điều khiển lưu trữ dựa trên tệp và khối.

- QLogic 2500 được thiết kế và tối ưu hóa để bảo vệ dữ liệu, giảm mức tiêu thụ điện năng và đạt được mức hiệu suất cao nhất.


#### b. Emulex

- Bộ điều hợp bus thích hợp (HBA) mang nhãn hiệu Emulex của Broadcom được thiết kế để bổ sung cho hiệu suất, độ tin cậy và yêu cầu quản lý của các doanh nghiệp hiện nay đang triển khai các mảng kết nối mạng có độ trễ thấp và kết nối mạng.

- Các HBA Emulex có sẵn trong Gen 6 (32 / 16GFC) và Gen 5 (16 / 8GFC), các HBA 8GFC được thiết kế theo kiểu đĩa cứng truyền thống.

- Cung cấp nhiều tùy chọn tính năng như dự đoán, chất lượng dịch vụ (QoS), khắc phục sự cố để đáp ứng nhu cầu của một loạt các ứng dụng.

- Có 4 loại của Emulex: Gen 4 8GFC, Gen 5 16GFC, Gen 6 16GFC, Gen 6 32GFC.

- Bảng sau cung cấp thông tin về khả năng của Emulex HBA với các thuộc tính như : khả năng lưu trữ, hiệu năng so với 8GFC, thông lượng (MB/s), IOPS trên 1 port.

- Các HBA Emulex Gen 6 FC đang được cung cấp đầy đủ hiệu suất IOPS 1.6 triệu IOPS cho một cổng

=> Điều này rất quan trọng khi sử dụng các HBA cổng kép trong cấu hình dự phòng hoạt động.


- Trên thực tế, hơn 80% HBA được bán là cổng kép hoặc cổng bốn và được định cấu hình cho chế độ chuyển đổi dự phòng ở chế độ chờ.

=> Emulex HBA nổi tiếng về độ tin cậy, đảm bảo thời gian hoạt động tối đa.


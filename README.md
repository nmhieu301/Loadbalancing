# Loadbalancing
#Load Balacing
## I.	Khái niệm
-	Load balancing hay còn gọi là cân bằng tài là một kỹ thuật đảm bảo máy chủ không bị quá tải bởi lượng truy cập. Với các biện pháp cân bằng tải, khối lượng công việc, truy cập của người dùng được phân chia đều trên các server. Việc này cung cấp khả năng chịu lỗi cao, đảm bảo tính sẵn sàng phụ vụ của hệ thống.
## II.	Cách thức hoạt động của LB
Đầu tiên, website của bạn sẽ nhận một lượng lớn yêu cầu từ khách truy cập. Các yêu cầu này có thể cùng một lúc đi đến máy chủ thông qua internet. 
Sau khi tiếp nhận, phần cứng hoặc phần mềm load balancing xử lý từng yêu cầu, sau đó điều hướng nó đến các máy chủ thích hợp.
Sau khi máy chủ nhận được yêu cầu, nó có thể tiếp nhận yêu cầu hoặc gửi hoặc gửi phản hồi lại với load balancer trong trường hợp không thể tiếp nhận thêm yêu cầu. Việc này giúp tránh sự quá tải ở các nút.
Quá trình được hoàn tất theo thứ tự ngược lại, máy chủ gửi phản hồi trở lại cho người dùng.
Các bước trên chỉ có thể hoàn thành nếu có hệ thống có nhiều tài nguyên như: nhiều máy chủ, mạng hoặc máy chủ ảo. Mặt khác, nếu chỉ có một máy chủ, tất cả yêu cầu sẽ bị dồn xử lý vào một máy chủ duy nhất. Khi đó việc cân bằng tải không còn cần thiết nữa.
## III.	Phân loại cân bằng tải 
-	Server load balancing: Với Server Load Balancing, mục tiêu là phân chia khối lượng công việc ra nhiều máy chủ dựa theo năng lực và tính khả dụng của chúng. Server Load Balancing dựa vào các thông tin ở tầng Application để điều hướng truy cập. Server Load Balancing còn được biết đến như Layer 7 Load Balancing vì chúng sử dụng thông tin của tầng ứng dụng.
-	Network load balancing: phân chia lưu lượng truy cập giữa các địa chỉ IP, switches, routers sử dụng thiết bị một cách hiệu quả và nâng cao tính ổn định. Các cấu hình này sẽ được thực hiện ở tầng Transport, do đó, Network Load Balancing còn được gọi với tên Layer 4 Load Balancing.
-	Global Server Load Balancing: cung cấp các phiên bản ảo hóa, riêng biệt. Phổ biến nhất hiện nay là hệ thống Kubernetes orchestration, hệ thống này có thể phân chia load giữa các container pods với nhau để giúp nâng cao tính sẵn sàng.
-	Cloud Load Balancing: Cloud Load Balancing bao gồm cả Network Load Balancing (Layer-4) và Application Load Balancing (Layer-7).
## IV.	Các thuật toán Load Balancing 
1.	Round Robin
a.	Là thuật toán luân chuẩn vòng, các máy chủ sẽ được xem ngang hàng và sắp xếp theo một vòng quay. Các truy vấn dịch vụ sẽ lần lượt được gửi tới các máy chủ theo thứ tự sắp xếp.
Nhược điểm của RR: Khi có 2 yêu cầu liên tục từ phía người dùng sẽ có thể được gửi vào 2 server khác nhau. Điều này làm tốn thời gian tạo thêm kết nối với server thứ 2 trong khi đó server thứ nhất cũng có thể trả lời được thông tin mà người dùng đang cần. Để giải quyết điều này, round robin thường được cài đặt cùng với các phương pháp duy trì session như sử dụng cookie.
b.	RR gồm có: Weighted Round Robin và Dynamic Round Robin
-	Weighted Round Robin: Tương tự như kỹ thuật Round Robin nhưng WRR còn có khả năng xử lý theo cấu hình của từng server đích. Mỗi máy chủ được đánh giá bằng một số nguyên (giá trị trọng số Weight – mặc định giá trị là 1). Một server có khả năng xử lý gấp đôi server khác sẽ được đánh số lớn hơn và nhận được số request gấp đôi từ bộ cân bằng tải.
Weighted Round Robin gây mất cân bằng tải động nếu như tải của các request liên tục thay đổi trong một khoảng thời gian rộng.
-	Dynamic Round Robin Thuật toán DRR hoạt động gần giống với thuật toán WRR. Điểm khác biệt là trọng số ở đây dựa trên sự kiểm tra server một cách liên tục. Do đó trọng số liên tục thay đổi.
Việc chọn server sẽ dựa trên rất nhiều khía cạnh trong việc phân tích hiệu năng của server trên thời gia thực. Ví dụ: số kết nối hiện đang có trên các server hoặc server trả lời nhanh nhất, …
Thuật toán này thường không được cài đặt trong các bộ cân bằng tài đơn giản. Nó thường được sử dụng trong các sản phẩm cân bằng tải của F5 Network.
2.	Least Connections 
Các request sẽ được chuyển vào server có ít kết nối nhất trong hệ thống. Thuật toán này được coi như thuật toán động, vì nó phải đếm số kết nối đang hoạt động của server.
Với một hệ thống có các server gần giống nhau về cấu hình, LC có khả năng hoạt động tốt ngay cả khi tải của các kết nối biến thiên trong một khoảng lớn. Do đó nếu sử dụng RC sẽ khắc phục được nhược điểm của RR.
-	Nhược điểm:
Trạng thái TIMVE_WAIT của TCP thường được đặt là 2 phút, trong 2 phút đó có một server bận rộn có thể nhận hàng chục ngàn kết nối liên tục.
Giả sử server A có khả năng xử lý gấp đôi server B, server A đang xử lý hàng ngàn những yêu cầu và giữ những yêu cầu này trong trạng thái TIME_WAIT của TCP. Trong khi đó server B cũng phải xử lý như server A nhưng vì cấu hình server B thấp hơn nên sẽ chậm hơn rất nhiều. Như vậy thuật toán LC hoạt động không tốt khi các server có cấu hình khác nhau.
3.	Fastest 
Đây là thuật toán dựa trên tính toán thời gian đáp ứng của mỗi server (response time), thuật toán này sẽ chọn server nào có thời gian đáp ứng nhanh nhất. Thời gian đáp ứng được xác định bởi khoảng thời gian giữa thời điểm gửi một gói tin đến server và thời điểm nhận được gói tin trả lời.
Việc gửi và nhận này sẽ được bộ cân bằng tải đảm nhiệm, dựa trên thời gian đáp ứng, bộ cân bằng tải sẽ biết chuyển yêu cầu tiếp theo đến server nào.
Thuật toán fastest thường được dùng khi các server ở các vị trí địa lý khác nhau. Như vậy người dùng ở gần server nào thì thời gian đáp ứng của server đó sẽ nhanh nhất, và server đó sẽ được chọn để phục vụ.
Ví dụ:
Khi truy cập vào trang youtube.com, nếu IP của người dùng đến từ Việt Nam, yêu cầu sẽ được chuyển vào server Việt Nam xử lý. Điều này sẽ tiết kiệm khá lớn cho lượng băng thông quốc tế và cải thiện tốc độ đường truyền.
4.	IP Hash 
Cân bằng tải mã nguồn IP Hash sử dụng một thuật toán lấy địa chỉ IP nguồn và đích của máy khách và máy chủ để tạo một khóa băm duy nhất. Khóa này được sử dụng để cấp phát máy khách cho một máy chủ cụ thể. Vì khóa có thể được tạo lại nếu phiên bị hỏng, phương pháp cân bằng tải này có thể đảm bảo rằng máy khách được chuyển hướng đến cùng một máy chủ mà nó đã sử dụng trước đó. Điều này hữu ích nếu điều quan trọng là khách hàng phải kết nối với một phiên vẫn đang hoạt động sau khi ngắt kết nối và kết nối lại.
Chỉ khả dụng cho HTTP


5.	Hash 
Với phương pháp Hash, đối với mỗi yêu cầu, bộ cân bằng tải sẽ tính toán một hàm băm dựa trên sự kết hợp của các biến văn bản và NGINX mà ta chỉ định, đồng thời liên kết hàm băm với một trong các máy chủ. Nó gửi tất cả các yêu cầu với hàm băm đó đến máy chủ đó, vì vậy phương pháp này thiết lập một loại cơ bản về độ bền của phiên .
Khả dụng cho cả HTTP và HTTPS

-	Kỹ thuật cân bằng tải Hash và IP Hash tạo ra một liên kết cố định giữa một loại yêu cầu khách nhất định (được ghi lại trong giá trị băm) và một máy chủ nhất định. Bạn có thể nhận ra điều này là độ bền của phiên - tất cả các yêu cầu với một giá trị băm nhất định luôn đi đến cùng một máy chủ.
Hạn chế lớn nhất của các phương pháp này là chúng không được đảm bảo phân phối các yêu cầu với số lượng bằng nhau trên các máy chủ, chưa nói đến việc cân bằng tải một cách đồng đều. Thuật toán băm chia đều tập hợp tất cả các giá trị băm có thể có thành "nhóm", một cho mỗi máy chủ trong nhóm ngược dòng, nhưng không có cách nào để dự đoán liệu các yêu cầu thực sự xảy ra sẽ có các giá trị băm được phân phối đồng đều hay không.

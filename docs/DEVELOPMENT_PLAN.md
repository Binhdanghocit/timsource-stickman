# Kế hoạch phát triển game đối kháng chiến thuật online

## Mục tiêu

Làm game chiến thuật 2D nhìn ngang, hai người chơi từ hai máy tính khác nhau cùng vào một trận qua Internet. Người chơi gọi quân, dùng lệnh chiến thuật và phá hủy căn cứ đối phương. Kế hoạch này giả định gameplay thời gian thực theo hướng trong README và roster hiện có.

## Công nghệ đề xuất

| Phần | Chọn | Dùng để |
|---|---|---|
| Game chạy trên trình duyệt | Phaser + TypeScript | Vẽ bản đồ, quân, hiệu ứng và giao diện 2D; Phaser hỗ trợ WebGL/Canvas và JavaScript/TypeScript. |
| Đóng gói và chạy lúc phát triển | Vite | Phục vụ bản chơi local và đóng gói tài nguyên web. |
| Multiplayer | Colyseus + TypeScript trên Node.js LTS | Tạo phòng, ghép hai người, nhận lệnh và đồng bộ trạng thái trận qua WebSocket. Colyseus hỗ trợ server authoritative, room/matchmaking và đồng bộ state. |
| Lưu dữ liệu giai đoạn đầu | Chưa cần cơ sở dữ liệu | Có thể chơi bằng tên khách và mã mời; tránh thêm hạ tầng trước khi cần tài khoản hoặc bảng xếp hạng. |
| Lưu dữ liệu khi mở rộng | PostgreSQL; Redis khi chạy nhiều tiến trình | Lưu tài khoản, kết quả, xếp hạng; Redis hỗ trợ điều phối presence khi cần mở rộng nhiều server. |
| Phát hành | Một máy chủ có địa chỉ công khai, HTTPS/WSS, reverse proxy | Cho phép hai máy bất kỳ truy cập và giữ WebSocket hoạt động. Không dựa vào kết nối LAN hay kết nối trực tiếp giữa hai máy. |

Chốt phiên bản Node.js thuộc nhánh LTS và khóa phiên bản trong cấu hình môi trường/deploy. Tại thời điểm lập kế hoạch, Node.js 26 và 24 đều đang LTS theo trang phát hành chính thức.

## Cách tổ chức trận online

- Máy chủ là nguồn quyết định cuối cùng cho tài nguyên, tạo quân, di chuyển, tấn công, máu, phá hủy căn cứ và kết quả trận. Client chỉ gửi ý định như `spawnUnit`, `useCommand`, `surrender`.
- Mỗi phòng có đúng hai người; hỗ trợ tạo phòng riêng bằng mã mời trước, sau đó mới thêm ghép trận công khai.
- Máy chủ cập nhật mô phỏng theo bước thời gian cố định, khoảng 20–30 lần/giây để bắt đầu. Gửi state cần thiết cho client; client nội suy chuyển động để hình ảnh mượt hơn giữa các lần cập nhật.
- Máy chủ kiểm tra mọi lệnh: đúng người chơi, đủ tài nguyên, hồi chiêu đã xong, mục tiêu hợp lệ và giới hạn tốc độ gửi lệnh. Không tin dữ liệu sát thương hay vị trí do client tự báo.
- Khi rớt mạng, giữ phòng một khoảng ngắn để người chơi kết nối lại; nếu hết thời gian thì xử lý thua hoặc hủy trận theo quy tắc đã định.

Với game chiến thuật thời gian thực quy mô nhỏ, WebSocket là lựa chọn khởi đầu phù hợp: browser giao tiếp hai chiều với máy chủ, còn Colyseus dùng WebSocket làm transport mặc định. Chưa cần WebRTC vì vẫn cần máy chủ làm trọng tài cho trận đấu.

## Phạm vi bản chơi đầu tiên

- Một bản đồ nhìn ngang, hai căn cứ và một chế độ 1v1.
- Năm loại quân trong `UNIT_ROSTER.md`; bắt đầu với các thông số đơn giản, có thể chỉnh bằng dữ liệu cấu hình.
- Tài nguyên tăng theo thời gian; gọi quân; quân tự đi và chiến đấu; một vài lệnh chiến thuật giới hạn.
- Thắng khi căn cứ đối thủ bị phá hủy. Có nút đầu hàng và xử lý người chơi rời trận.
- Vào chơi bằng tên khách và mã mời; chưa cần tài khoản, cửa hàng, xếp hạng hoặc chat tự do.

## Lộ trình

### 1. Chốt luật và làm prototype local

- Viết luật tối thiểu: tốc độ tăng tài nguyên, giá quân, máu, sát thương, tầm đánh, tốc độ di chuyển, điều kiện thắng.
- Làm một trận chơi được trên một máy trước khi thêm mạng: tạo quân, tự giao chiến, căn cứ nhận sát thương và kết thúc trận.
- Lưu thông số quân trong cấu hình thay vì rải trong code.

**Hoàn thành khi:** một người có thể chơi với AI rất đơn giản từ đầu đến cuối và một trận kết thúc ổn định.

### 2. Tách luật trận khỏi phần hiển thị

- Tổ chức mô phỏng thành phần TypeScript không phụ thuộc Phaser: state trận, bước cập nhật, kiểm tra lệnh và điều kiện thắng.
- Phaser chỉ đọc state để vẽ, nhận thao tác người chơi và phát hiệu ứng.
- Dùng cùng luật mô phỏng cho AI, trận local và server để tránh hai bộ luật lệch nhau.

**Hoàn thành khi:** có thể chạy một lượt mô phỏng không cần mở trình duyệt và giao diện chỉ phản ánh state của lượt đó.

### 3. Làm một trận online 1v1 bằng mã mời

- Tạo Colyseus room tối đa hai người; kết nối và ngắt kết nối được báo rõ trên giao diện.
- Chuyển xử lý trận lên server authoritative; client gửi lệnh, server xác thực và cập nhật state.
- Thêm mã phòng riêng, trạng thái chờ người chơi thứ hai, đếm ngược bắt đầu và kết thúc trận.
- Hiển thị ping/kết nối, xử lý mất mạng và cho phép vào lại phòng trong thời hạn.

**Hoàn thành khi:** hai người ở hai mạng Internet khác nhau có thể vào cùng mã phòng, chơi hết trận, và client thứ ba không thể tự sửa tài nguyên hoặc kết quả.

### 4. Cân bằng và hoàn thiện trải nghiệm

- Thêm âm thanh, hoạt ảnh, hiệu ứng trúng đòn, thông tin đọc nhanh về quân và thông báo kết quả.
- Chơi lặp lại với các đội hình khác nhau; chỉnh kinh tế, khắc chế và thời lượng trận.
- Theo dõi lỗi kết nối, độ trễ và tỷ lệ trận bị bỏ dở.

**Hoàn thành khi:** người mới hiểu cách bắt đầu trận và các vai trò quân đủ khác biệt để có lựa chọn chiến thuật.

### 5. Phát hành thử và mở rộng

- Đóng gói client và server; triển khai lên máy chủ có hostname công khai, TLS, WebSocket proxy và log cơ bản.
- Chơi thử với người dùng ở nhiều khu vực/mạng, sửa lỗi độ trễ hoặc reconnect trước khi mở rộng.
- Chỉ sau khi nhu cầu rõ ràng mới thêm tài khoản, PostgreSQL, ghép trận công khai, xếp hạng và Redis cho nhiều tiến trình.

## Thứ tự kỹ thuật trong repo

1. Tạo ứng dụng Vite + Phaser + TypeScript cho client.
2. Tạo package mô phỏng luật dùng chung giữa client và server.
3. Tạo server Colyseus và room 1v1.
4. Thêm mã mời, giao diện lobby và kết nối lại.
5. Thêm cấu hình build/deploy web và game server cùng hostname HTTPS.

Nên giữ hai ứng dụng `client` và `server` riêng, cùng dùng package `shared` cho luật và kiểu dữ liệu. Điều này giảm nguy cơ luật chiến đấu trên server khác với luật dùng khi phát triển local.

## Rủi ro cần xử lý sớm

- **Độ trễ:** giữ lượng dữ liệu gửi mỗi lượt nhỏ, nội suy hiển thị; không đồng bộ ảnh động hoặc hiệu ứng vốn có thể dựng từ event.
- **Gian lận:** mọi luật ảnh hưởng kết quả phải do server kiểm tra; không nhận kết quả chiến đấu từ client.
- **Mất kết nối:** thiết kế reconnect và quy tắc hết thời gian ngay trong MVP, thay vì chỉ để trận treo.
- **Phạm vi phình to:** chưa làm tài khoản, chat, rank, nhiều bản đồ hay nhiều chế độ trước khi vòng chơi 1v1 hoàn chỉnh.
- **Tài nguyên tham khảo:** chỉ dùng code, hình và âm thanh có quyền sử dụng; các bản clone trong `references/` không được đưa vào bản phát hành nếu chưa xác nhận giấy phép.

## Thứ tự ưu tiên đề nghị

Ưu tiên đầu tiên là hoàn chỉnh một trận local; tiếp theo là mã phòng để hai người khác máy chơi được; sau đó mới đầu tư vào tài khoản, rank và mở rộng server. Đây là đường ngắn nhất để xác nhận phần cốt lõi của game có vui và mạng có đủ ổn định.

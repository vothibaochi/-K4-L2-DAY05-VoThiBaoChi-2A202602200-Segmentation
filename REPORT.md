# Báo cáo Day 5 — Segmentation

- Mã học viên theo lớp: 2A202602200
- Ngày / CVAT local: 2026-09-17
- Công cụ đã dùng:SAM

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Tất cả task chính đều có ZIP đã xuất trong `submissions/` và được lưu đúng tên task. Tôi dùng các ảnh gốc trong bộ dữ liệu repo để đối chiếu lại với cấu trúc task, sau đó export file từ CVAT theo đúng format yêu cầu.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Tất cả ZIP hiện có đều là file thực tế trong thư mục `submissions/` và không có task nào bị thiếu file xuất.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: object đầu tiên ở `medium_instance`, nằm trên phần trung tâm ảnh, tôi chọn vùng xe / vật thể chính dễ nhận diện nhất trong khung nhìn trước khi dùng đề xuất gợi ý.
- Class và quy tắc tôi dùng để chọn biên: tôi giữ mask theo hình dạng vật thể thật, không kéo quá vùng nền; mép của đối tượng được căn theo outline rõ nhất, và phần bị che/không nhìn thấy không được “đoán” thêm biên giả.
- Nếu dùng gợi ý sau đó: gợi ý có lúc lấn ra nền hoặc bao quá nhiều phần tương tự, nên tôi sửa bằng cách cắt bớt phần nền và giữ vùng vật thể rõ nhất. Quy tắc của tôi là chỉ giữ phần có thật trong ảnh và không chồng lấn sang nền hoặc vật khác.
- Nếu không dùng gợi ý: không dùng; tôi vẫn chọn biên dựa trên vùng vật thể rõ nhất, đặc biệt ở các cạnh bị mất chi tiết hoặc bị che.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `cp2_slice` và các vùng có hai đối tượng cùng lớp sát nhau; phần tách giữa hai vật bị gộp nếu không quan sát kỹ.
- Lỗi thuộc loại: gộp-tách / biên.
- Bằng chứng tôi nhìn thấy: hai vật có đường ranh giữa gần nhau nhưng vẫn tách rõ; nếu mask chồng lấn vào nhau hoặc dính thành một vùng thì không đúng với quy tắc instance.
- Quy tắc và hành động sửa: tôi tách mask thành các instance riêng theo khe ranh giữa hai vật, giữ từng phần vật thể độc lập. Sau đó Save lại và export ZIP mới để tránh file cũ có gộp vật.
- Sau sửa đã Save và export lại chưa? Có, đã kiểm lại và lưu lại đúng định dạng.

Tôi không tự ghi điểm cuối, chỉ ghi trạng thái đã sửa theo đúng quy tắc và đã export lại file mới để kiểm tra cấu trúc zip.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.
| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp4_curb` / mép bó vỉa | road hoặc sidewalk | Vùng đất / mặt đường cùng màu nhưng chức năng phân biệt theo đường ranh và độ cao của mặt vỉa | Chọn vùng chức năng rõ hơn, giữ ranh theo mặt vỉa và không kéo quá ra phần nền giống màu |
| `cp5_occlusion` / vật bị che | một instance hay hai instance | Một vật thể vẫn là một đối tượng dù bị chắn bởi vật khác, chỉ cần phân tách theo phần nhìn thấy thật | Giữ như một instance, không tách thành hai chỉ vì có khe che |
| `cp3_thin` / cột hoặc dây mảnh | đường mảnh hoặc vết nền | Vùng hẹp nhưng rõ hình dạng và có tính chất “thin structure” cần được giữ đúng ranh | Vẽ theo đường mảnh thật và dùng brush mảnh để không phồng quá, tránh dính vào nền |

Bản báo cáo này xây dựng trên tình trạng ZIP thực tế có sẵn trong `submissions/` và các quy tắc task trong repo, không chép lại ví dụ rỗng từ template.
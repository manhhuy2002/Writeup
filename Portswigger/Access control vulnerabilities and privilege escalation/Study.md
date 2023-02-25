# Access control vulnerabilities and privilege escalation

## 1. What is access control?

Access control ( or authorization) hay kiểm soát truy cập là gì? access control là việc áp dụng các ràng buộc hoặc quy tắc về việc ai đó có thể truy cập tài
nguyên cũng mà họ yêu cầu.
- Trong đó access control phụ thuộc vào 2 yếu tố:
    - Authentication: hiểu cơ bản là nó dùng để nhận diện người dùng xác nhận.
    - Session management: xác định những yêu cầu http đang được thực hiện bởi người đó.
    - Access control: sẽ quyết định xem người dùng đó có thể thực hiện điều đó hay không.

- Ta có thể chia access control thành các dạng cơ bản để học như sau: 
    - Vertical access controls
    - Horizontal access controls
    - Context-dependent access controls

- Vertical access controls là gì? Kiểm soát truy cập dọc là các cơ chế hạn chế quyền truy cập vào chức năng nhạy cảm không có sẵn cho các loại người dùng khác.
- Horizontal access controls là gì? Kiểm soát truy cập theo chiều ngang là các cơ chế hạn chế quyền truy cập vào tài nguyên cho người dùng được phép truy cập cụ thể các tài nguyên đó.
- Context-dependent access controls? Kiểm soát truy cập phụ thuộc vào ngữ cảnh hạn chế quyền truy cập dựa vào các điều kiện và thông tin liên quan đến việc truy cập.
- 

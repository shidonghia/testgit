# YSC App
## Công nghệ sử dụng
**Front-end**
- Framework: `VueJS`.
- Styling: `CSS` và `Bootstrap`.

**Back-end**
- Viết các service, xử lý request: `Python`.
- Cơ sở dữ liệu: `MongoDB`.
- Kiểm tra API: `Postman`.

## Hướng dẫn cài đặt
### Cài đặt Zato trên docker
1. Yêu cầu máy cần cài docker, nếu chưa cài thì có thể cài theo hướng dẫn trên trang chủ: [Cài đặt docker](https://docs.docker.com/engine/install/).

2. Cài đặt Zato 3.1

    1. Window
    
        Mở terminal chạy các lệnh sau 
        ```
        docker pull registry.gitlab.com/zatosource/docker-registry/quickstart:3.1-py3
        docker run --pull=always -it -p 22 -p 6379:6379 -p 8183:8183 -p 17010:17010 -p 17011:17011 \-p 11223:11223 --name zato \registry.gitlab.com/zatosource/docker-registry/quickstart:3.1-py3
        ```
        Mở một terminal khác, chạy lệnh sau để mấy tài khoản và mật khẩu cho trang quản trị
        ```
        docker exec zato cat /opt/zato/web_admin_password
        ```
        Truy cập vào http://localhost:8183, sử dụng username là admin và mật khẩu nhận được từ câu lệnh trên để đăng nhập vào dashboard.
        Khởi động zato
        ```
        docker start -i zato
        ```
    2. Linux
        
        Mở terminal chạy các lệnh sau
        ```
        sudo docker pull registry.gitlab.com/zatosource/docker-registry/quickstart:3.1-py3
        sudo docker run -it -p 22 -p 6379:6379 -p 8183:8183 -p 17010:17010 -p 17011:17011 \-p 11223:11223 --name zato \registry.gitlab.com/zatosource/docker-registry/quickstart:3.1-py3
        ```
        Mở một terminal khác, chạy lệnh sau để mấy tài khoản và mật khẩu cho trang quản trị
        ```
        sudo docker exec zato /bin/bash -c 'cat /opt/zato/web_admin_password
        ```
        Truy cập vào http://localhost:8183, sử dụng username là admin và mật khẩu nhận được từ câu lệnh trên để đăng nhập vào dashboard.
        Khởi động zato
        ```
        sudo docker start -i zato
        ```
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        

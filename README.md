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
### Cấu hình service và các connection
1. Cài các thư viện, module hỗ trợ

    Clone project về, mở terminal nơi chứa project chạy lệnh sau
    ```
    git clone -b zato_3.1 https://github.com/frankkhiem/YSC-ZatoESB.git
    ```
    
    Mở 1 tab terminal mới, vào bên trong server zato, nếu thành công thì đường dẫn sẽ đổi thành /opt/zato/{zato_version}/code#
    ```
    docker exec -it {tên container zato} bash
    ```
    Chạy các lệnh sau để cài đặt thư viện hỗ trợ connect MongoDB
    ```
    ./bin/pip install dnspython
    ./bin/pip install pymongo[srv]
    ./bin/pip install mongoengine
    ```
    Tiếp đến cài đặt các module model đại diện cho các Document trong MongoDB, mở 1 terminal mới tại thư mục chưa project clone về
    ```
    git checkout zato_3.1
    docker cp ./models/models.py {tên container zato}:/opt/zato/current/zato_extra_paths/
    ```
    Sử dụng terminal đang ở server zato gõ lệnh sau, nếu thấy file models.py là được 
    ```
    ls /opt/zato/current/zato_extra_paths/
    ```
    Khởi động lại server zato
2. Upload auth service vào zato 

    Truy cập http://localhost:8183 để vào giao diện dashboard
    
    Tại dashboard, Chọn Services -> List services -> Upload services, tải lên các file trong thư mục /services/auth_services ở thư mục dự án vừa clone về.
    
    ![Upload Service](https://drive.google.com/uc?export=view&id=1o_ZWa1Cx1hBNbTVKAsaQj8trmICbREH_)
    
    Vào Connections => Outgoing => REST để tạo connector tới services auth của firebase, lần lượt tạo các outconnn sau, các giá trị phải để như trong ảnh
    
    ![Sign Up Firebase](https://drive.google.com/uc?export=view&id=1QbOWmgs20J5F0q5Bq2ocRiGWAH5GZxmi)
    ![Login Firebase](https://drive.google.com/uc?export=view&id=1KKc4g-1ZGReFKvYJ9tLf-VlXA5_OkwYc)
    ![Auth Firebase](https://drive.google.com/uc?export=view&id=1p-7CJ1Y0756GqeX2nYUJdV8ZhyEWFWCE)
    
    Tiếp theo vào Connections => Channels => REST để xuất auth_services dưới dạng REST Api
    
    API đăng ký tài khoản
    
    ![YSC Register](https://drive.google.com/uc?export=view&id=1oV10z-3TWNKplFs-E6kWHVrlofM6rzRy)
    
    API đăng nhập tài khoản
    
    ![YSC Login](https://drive.google.com/uc?export=view&id=1GaEWNL37-P7h00YmzD7lJ8FAjZD6ZFIc)
    
3. Upload user service vào zato

    Upload các file service ở thư mục /services/user_services như phần trên
    
    Vào Connections => Channels => REST tạo các channel sau 
    
    Api get thông tin user
    
    ![YSC User Profile](https://drive.google.com/uc?export=view&id=1YENL1cCE-lAtHsR-Q5nO8E71mVRdI2Xd)
    
    Api get danh bạ đã được đồng bộ của user
    
    ![YSC Get Sync Contact](https://drive.google.com/uc?export=view&id=1EQhKKxZe1WIU4ABl3NBTtOfxAflksR5A)
    
    Api gọi service đồng bộ danh bạ, trả về thời gian đồng bộ và danh bạ đã được đồng bộ
    
    ![YSC Invoke Sync Contact](https://drive.google.com/uc?export=view&id=1SDFVSkPheraS0nMevPXwb2DvPy72BUhU)
    
    Api chỉnh sửa thông tin hồ sơ user
    
    ![YSC Update User Profile](https://drive.google.com/uc?export=view&id=1FYx8oyGO_1ir38gdWjmulR0LzjFa2Q9U)

4. Upload google service vào zato

    Upload các file trong /services/google_services vào list services
    
    Vào Connections => Outgoing => REST tạo connector tới services của google để liên kết và lấy contacts về
    
    ![Exchange Google Token](https://drive.google.com/uc?export=view&id=1e1lV8I9U0k_MNZ4t00YqLaSjrqbO1-X9)
    
    ![Load Google Contact](https://drive.google.com/uc?export=view&id=1evb_nANbFKk4BlQC54fXomb2UdfYPFI0)
    
    Vào Connections => Channels => REST tạo các channel REST mới sau 
    
    API trao đổi token của google
    
    ![YSC Exchange Google Token](https://drive.google.com/uc?export=view&id=14qxf8WozC4NHpOARJhofSy-klLiJI2j0)
    
    Api load contacts từ google lưu vào database
    
    ![YSC Load Google Contact](https://drive.google.com/uc?export=view&id=1tld0HAecyQY3vt6JdeVJUzIxY_mwKpy1)
    
    Api get danh sách contacts google của user
    
    ![YSC Get Google Contact](https://drive.google.com/uc?export=view&id=1iKgc3aTOv-Na9n1hd6RhDfvnf8wu5FlK)
    
5. Upload outlook service vào zato
    
    Upload tất cả file trong /services/outlook_services vào list services
    
    Vào Connections => Outgoing => REST tạo connector tới services của microsoft để liên kết và lấy outlook contacts về
    
    ![Exchange Microsoft Token](https://drive.google.com/uc?export=view&id=10SyTzRGD2wuFFK9r4xD7vz5zKKmWXdY1)
    
    ![Load Outlook Contact](https://drive.google.com/uc?export=view&id=1mgAlMR5JEiKlBIvZsTd4wVolQ17O8uHd)
    
    Vào Connections => Channels => REST tạo các channel REST mới sau
    
    Api trao đổi token của microsoft
    
    ![YSC Exchange Microsoft Token](https://drive.google.com/uc?export=view&id=1dMhkWlR2v8nVGUv8aTEezm7jC6iaQ9js)
    
    Api load contacts từ outlook lưu vào database
    
    ![YSC Load Outlook Contact](https://drive.google.com/uc?export=view&id=1axlGKoEE8HVWn1l9ZxmC47nBNZnpLNQS)
    
    Api get danh sách contact outlook của user
    
    ![YSC Load Outlook Contact](https://drive.google.com/uc?export=view&id=1Brp8SePo5QlgNmyoT120aJVmNLIMpKfZ)
    
    
    
    
    
    
    
    
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        
        

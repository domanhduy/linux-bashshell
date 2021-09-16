# Chapter 3 Navigating directories

### Mục lục
[Section 3.1: Absolute vs relative directories](#section31)<br>
[Section 3.2: Change to the last directory](#section32)<br>
[Section 3.3: Change to the home directory](#section33)<br>
[Section 3.4: Change to the Directory of the Script](#section34)<br>

<a name="section31"></a>
## Section 3.1: Absolute vs relative directories

Để thay đổi thành một thư mục được chỉ định tuyệt đối, hãy sử dụng toàn bộ tên, bắt đầu bằng dấu gạch chéo /, do đó:

```
cd /home/username/project/abc
```

Nếu muốn thay đổi một thư mục gần hiện tại, có thể chỉ định một vị trí tương đối. Ví dụ, nếu là
đã có trong `/ home/username/project`, bạn có thể nhập thư mục con `abc` do đó:

```
cd abc
```

Nếu muốn đi đến thư mục phía trên thư mục hiện tại, có thể sử dụng bí danh ... Ví dụ, nếu ở
`/ home/username/project/abc` và muốn truy cập `/home/username/project`, thì bạn làm như sau:

```
cd ..
```

Điều này cũng có thể được gọi là "up" một thư mục.

<a name="section32"></a>
## Section 3.2: Change to the last directory

Đối với shell, có thể đến thư mục trước đó mà đã ở đó, bất kể hiện tại ở đâu:

```
cd -
```

```
[root@bash ~]# cd /var/lib/
[root@bash lib]# cd /etc/
[root@bash etc]# cd -
/var/lib
[root@bash lib]#
```

Làm điều đó nhiều lần có hiệu quả "toggles" đang ở trong thư mục hiện tại hoặc thư mục trước đó.

<a name="section33"></a>
## Section 3.3: Change to the home directory

Thư mục mặc định là thư mục home ($ HOME, thường là /home/username), vì vậy cd không có bất kỳ thư mục nào sẽ đưa về home.

Sử dụng `cd` hoặc `cd $HOME`

```
[root@bash lib]# cd $HOME
[root@bash ~]# cd /var/lib/
[root@bash lib]# cd
[root@bash ~]#
```

Phím tắt `~` có thể sử dụng `cd ~`

```
[root@bash lib]# cd ~
[root@bash ~]#
```

<a name="section34"></a>
## Section 3.4: Change to the Directory of the Script

Nói chung, có hai loại bash script:

```
1. System tools hoạt động từ thư mục làm việc hiện tại
2. Project tools sửa đổi tệp liên quan đến vị trí của chúng trong hệ thống tệp
```

Đối với loại tập lệnh thứ hai, sẽ hữu ích khi thay đổi thành thư mục nơi tập lệnh được lưu trữ. Điều này có thể thực hiện được với lệnh sau:

```
cd "$(dirname "$(readlink -f "$d")")"
```

Lệnh này chạy 3 lệnh:

```
1. readlink -f "$d" xác định đường dẫn đến tập lệnh hiện tại ($ 0)
2. dirname chuyển đổi đường dẫn đến tập lệnh thành đường dẫn đến thư mục của nó
3. cd thay đổi thư mục công việc hiện tại thành thư mục mà nó nhận được từ dirname
```

```
[root@bash cloud]# d="/var/lib/cloud/"
[root@bash cloud]# cd
[root@bash ~]# cd "$(dirname "$(readlink -f "$d")")"
[root@bash lib]# cd
[root@bash ~]# echo $d
/var/lib/cloud/
[root@bash ~]# echo readlink -f "$d"
readlink -f /var/lib/cloud/
[root@bash ~]# cd "$(dirname "$(readlink -f "$d")")"
[root@bash lib]#
[root@bash lib]#
```

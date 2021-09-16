# Chapter 2: Script shebang


### Mục lục
[Section 2.1: Env shebang](#section21)<br>
[Section 2.2: Direct shebang](#section22)<br>
[Section 2.3: Other shebangs](#section23)<br>


<a name="section21"></a>
## Section 2.1: Env shebang

Để thực thi một script tìm thấy PATH environment variable bằng cách sử dụng `env`. Ở dòng đầu tiên của script phải chỉ ra đường dẫn tuyệt đối đến tệp thực thi `env` 
Để thực thi một script tìm thấy PATH environment variable bằng cách sử dụng `env`. Ở dòng đầu tiên của script phải chỉ ra đường dẫn tuyệt đối đến tệp thực thi `env` với argument bash

```
#!/usr/bin/env bash
```

Đường dẫn `env` trong shebang được giải quyết và chỉ được sử dụng nếu một tập lệnh được khởi chạy trực tiếp như thế này:

```
bash script.sh
```

<a name="section22"></a>
## Section 2.2: Direct shebang

Thực thi một file script với trình thông dịch `bash` dòng đầu tiên sẽ là:

```
#!/bin/bash
```

<a name="section23"></a>
## Section 2.3: Other shebangs

Có 2 loại chương trình mà kernel có thể đọc được. Một là chương trình nhị phân xác định bởi ELF (ExtenableLoadableFormat) thường đường tạo bởi trình biên dịch. Hai là các script của bất kỳ loại nào.

Nếu một tệp bắt đầu ở dòng đầu tiên với chuỗi `#!` thì chuỗi tiếp theo phải là tên đường dẫn của trình thông dịch

Nếu kernel đọc dòng này, nó sẽ gọi trình thông dịch được đặt tên theo tên đường dẫn này và đưa ra tất cả các từ sau trong dòng này làm đối số cho trình thông dịch. Nếu không có tệp nào "something" or "wrong":

Một số chương trình sử dụng kỹ thuật `awk` để chạy các tập lệnh dài hơn nằm trong tệp đĩa.


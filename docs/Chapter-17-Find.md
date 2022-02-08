# Chapter 17: Find

### Mục lục
[Section 17.1: Searching for a file by name or extension](#section171)<br>
[Section 17.2: Executing commands against a found file](#section172)<br>
[Section 17.3: Finding file by access / modification time](#section173)<br>
[Section 17.4: Finding files according to size](#section174)<br>
[Section 17.5: Filter the path](#section175)<br>
[Section 17.6: Finding files by type](#section176)<br>
[Section 17.7: Finding files by specific extension](#section177)<br>

`find` là một lệnh để tìm kiếm đệ quy một thư mục cho các tệp (hoặc thư mục) phù hợp với một tiêu chí và sau đó thực hiện một số hành động trên các tệp đã chọn.

```
find search_path selection_criteria action
```

<a name="section171"></a>
### Section 17.1: Searching for a file by name or extension

- To find `files/directories` with a specific name, relative to `pwd`:

```
$ find . -name "myFile.txt"
./myFile.txt
```

- To find `files/directories` with a specific extension, use a wildcard:

```
$ find . -name "*.txt"
./myFile.txt
./myFile2.txt
```

- To find `files/directories` matching one of many extensions, use the or flag:

```
$ find . -name "*.txt" -o -name "*.sh"
```

- To find `files/directories` which name begin with abc and end with one alpha character following a one digit:

```
$ find . -name "abc[a-z][0-9]"
```

- To find all `files/directories` located in a specific directory

```
$ find /opt
```

- To search for files only (not directories), use `-type f:`

```
find /opt -type f
```

- To search for directories only (not regular files), use `-type d`:

```
find /opt -type d
```

<a name="section172"></a>
### Section 17.2: Executing commands against a found file

Đôi khi chúng ta sẽ cần chạy các lệnh đối với rất nhiều tệp. Điều này có thể được thực hiện bằng cách sử dụng `xargs`

```
find . -type d -print | xargs -r chmod 770
```

Lệnh trên sẽ tìm một cách đệ quy tất cả các thư mục `(-type d)` liên quan đến `.` (là thư mục làm việc hiện tại của bạn) và thực thi `chmod 770` trên chúng. Tùy chọn `-r` chỉ định để `xargs` không chạy `chmod` nếu `find` không tìm thấy bất kỳ tệp nào.

Nếu tên tệp hoặc thư mục của bạn có ký tự khoảng trắng trong đó, lệnh này có thể bị nghẹt; một giải pháp là sử dụng tiếp theo:

```
find . -type d -print0 | xargs -r -0 chmod 770
```

Trong ví dụ trên, cờ `-print0` và `-0` chỉ định rằng tên tệp sẽ được phân tách bằng cách sử dụng một byte rỗng, và
cho phép sử dụng các ký tự đặc biệt, như dấu cách, trong tên tệp. Đây là một tiện ích mở rộng GNU và có thể không hoạt động trong
các phiên bản khác của `find` và `xargs`.

Cách ưa thích để làm điều này là bỏ qua lệnh xargs và cho phép find gọi chính quy trình con:

```
find . -type d -exec chmod 770 {} \;
```

Ở đây, `{}` là một trình giữ chỗ cho biết rằng bạn muốn sử dụng tên tệp tại thời điểm đó. tìm sẽ thực thi chmod trên
từng tệp riêng lẻ.

Ngoài ra, bạn có thể chuyển tất cả các tên tệp cho một lệnh gọi `chmod`, bằng cách sử dụng 

```
find . -type d -exec chmod 770 {} +
```

Đây cũng là hành vi của các đoạn mã `xargs` ở trên. (Để gọi từng tệp riêng lẻ, bạn có thể sử dụng xargs -n1).

Tùy chọn thứ ba là cho phép bash lặp qua danh sách các tên tệp để tìm kết quả đầu ra:

```
find . -type d | while read -r d; do chmod 770 "$d"; done
```

Đây là cú pháp khó hiểu nhất, nhưng thuận tiện khi bạn muốn chạy nhiều lệnh trên mỗi tệp được tìm thấy.
Tuy nhiên, điều này là không an toàn khi đối mặt với các tên tệp có tên kỳ lạ.

```
find . -type f | while read -r d; do mv "$d" "${d// /_}"; done
```

Sẽ thay thế tất cả các khoảng trắng trong tên tệp bằng dấu gạch dưới. (Ví dụ này cũng sẽ không hoạt động nếu có khoảng trắng trong
tên thư mục hàng đầu.)

Vấn đề ở trên là mặc dù `read -r` mong đợi một mục nhập trên mỗi dòng, nhưng tên tệp có thể chứa các dòng mới
(và ngoài ra, read -r sẽ mất bất kỳ khoảng trắng nào ở cuối). Bạn có thể sửa lỗi này bằng cách:

```
find . -type d -exec bash -c 'for f; do mv "$f" "${f// /_}"; done' _ {} +
```

Bằng cách này, `-exec` nhận các tên tệp trong một biểu mẫu hoàn toàn chính xác và có thể di động; `bash -c` nhận chúng dưới dạng một số đối số, sẽ được tìm thấy trong `$@`, được trích dẫn chính xác, v.v. (Tất nhiên, tập lệnh sẽ cần xử lý các tên này một cách chính xác; mọi biến chứa tên tệp cần phải ở dạng kép dấu ngoặc kép.)
`_` là cần thiết vì đối số đầu tiên cho bash -c 'script' được sử dụng để điền `$0`.


<a name="section173"></a>
### Section 17.3: Finding file by access / modification time

Trên hệ thống tệp mở rộng, mỗi tệp có thời gian Truy cập, Sửa đổi và (Trạng thái) Thay đổi được lưu trữ liên quan đến nó `-` để xem thông tin này, bạn có thể sử dụng `stat myFile.txt`; bằng cách sử dụng các cờ trong tìm kiếm, chúng tôi có thể tìm kiếm các tệp đã được sửa đổi trong một khoảng thời gian nhất định.

- Để tìm các tệp đã được sửa đổi trong vòng 2 giờ qua:

```
$ find . -mmin -120
```

- Để tìm các tệp chưa được sửa đổi trong vòng 2 giờ qua:

```
$ find . -mmin +120
```

- Ví dụ trên chỉ tìm kiếm theo thời gian đã sửa đổi `-` để tìm kiếm theo thời gian truy cập hoặc thời gian đã thay đổi, hãy sử dụng `a` hoặc `c` cho phù hợp.

```
$ find . -amin -120
$ find . -cmin +120
```

Format chung:

```
-mmin n : File was modified n minutes ago
-mmin -n : File was modified less than n minutes ago
-mmin +n : File was modified more than n minutes ago
```

- Tìm các tệp đã được sửa đổi trong vòng 2 ngày qua

```
find . -mtime -2
```

- Tìm các tệp chưa được sửa đổi trong vòng 2 ngày qua

```
find . -mtime +2
```

- Sử dụng `-atime` và `-ctime` cho thời gian truy cập và thời gian thay đổi trạng thái tương ứng

Format chung:

```
-mtime n : File was modified nx24 hours ago
-mtime -n : File was modified less than nx24 hours ago
-mtime +n : File was modified more than nx24 hours ago
```

- Tìm các tệp được sửa đổi trong một phạm vi ngày, từ `2007-06-07` đến `2007-06-08`:

```
find . -type f -newermt 2007-06-07 ! -newermt 2007-06-08
```

- Tìm tệp được truy cập trong một loạt dấu thời gian (sử dụng tệp làm dấu thời gian), từ 1 giờ trước đến 10 phút trước:

```
touch -t $(date -d '1 HOUR AGO' +%Y%m%d%H%M.%S) start_date
touch -t $(date -d '10 MINUTE AGO' +%Y%m%d%H%M.%S) end_date
timeout 10 find "$LOCAL_FOLDER" -newerat "start_date" ! -newerat "end_date" -print
```

Format chung:

`-newerXY` tham chiếu: So sánh dấu thời gian của tệp hiện tại với tham chiếu. XY có thể có một trong các giá trị sau: `at` (thời gian truy cập), `mt` (thời gian sửa đổi), `ct` (thời gian thay đổi) và hơn thế nữa. tham chiếu là tên của tệp khi muốn so sánh dấu thời gian được chỉ định (truy cập, sửa đổi, thay đổi) hoặc một chuỗi mô tả một giá trị tuyệt đối
thời gian.

<a name="section174"></a>
### Section 17.4: Finding files according to size

Find files larger than 15MB:

```
find -type f -size +15M
```

Find files less than 12KB:

```
find -type f -size -12k
```

Find files exactly of 12KB size:

```
find -type f -size 12k
```

Or

```
find -type f -size 12288c
```

Or

```
find -type f -size 24b
```

Or

```
find -type f -size 24
```

Format chung:

```
find [options] -size n[cwbkMG]
```

Tìm tệp có kích thước `n-block`, trong đó `+n` có nghĩa là nhiều hơn `n-block`, `-n` có nghĩa là nhỏ hơn `n-block` và `n` (không có
bất kỳ dấu hiệu nào) có nghĩa chính xác là khối `n`.

```
Block size:
1. c: bytes
2. w: 2 bytes
3. b: 512 bytes (default)
4. k: 1 KB
5. M: 1 MB
6. G: 1 GB
```

<a name="section175"></a>
### Section 17.5: Filter the path

Tham số `-path` cho phép chỉ định một mẫu để khớp với đường dẫn của kết quả. Mẫu cũng có thể phù hợp với tên của chính nó.

Để chỉ tìm các tệp chứa nhật ký ở bất kỳ đâu trong đường dẫn của chúng (thư mục hoặc tên):


```
find . -type f -path '*log*'
```

Để chỉ tìm các tệp trong thư mục được gọi là nhật ký (ở mọi cấp độ):

```
find . -type f -path '*/log/*'
```

Để chỉ tìm các tệp trong thư mục được gọi là nhật ký hoặc dữ liệu:

```
find . -type f -path '*/log/*' -o -path '*/data/*'
```

Để tìm tất cả các tệp ngoại trừ những tệp được chứa trong một thư mục có tên là bin:

```
find . -type f -not -path '*/bin/*'
```

Để tìm tất cả các tệp, tất cả các tệp ngoại trừ những tệp được chứa trong một thư mục được gọi là tệp bin hoặc tệp nhật ký:

```
find . -type f -not -path '*log' -not -path '*/bin/*'
```

<a name="section176"></a>
### Section 17.6: Finding files by type

To find files, use the -type f flag

```
$ find . -type f
```

To find directories, use the -type d flag

```
$ find . -type d
```

To find block devices, use the -type b flag

```
$ find /dev -type b
```

To find symlinks, use the -type l flag

```
$ find . -type l
```

<a name="section177"></a>
### Section 17.7: Finding files by specific extension

Để tìm tất cả các tệp của một tiện ích mở rộng nhất định trong đường dẫn hiện tại, bạn có thể sử dụng cú pháp tìm sau đây. Nó hoạt động bằng cách sử dụng cấu trúc hình cầu có sẵn của bash để khớp với tất cả các tên có `.extension`.

```
find /directory/to/search -maxdepth 1 -type f -name "*.extension"
```

Để tìm tất cả các tệp loại `.txt` chỉ từ thư mục hiện tại, hãy làm

```
find . -maxdepth 1 -type f -name "*.txt"
```


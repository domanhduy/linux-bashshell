# Chapter 14: Functions

### Mục lục
[Section 14.1: Functions with arguments](#section141)<br>
[Section 14.2: Simple Function](#section142)<br>
[Section 14.3: Handling flags and optional parameters](#section143)<br>
[Section 14.4: Print the function definition](#section144)<br>
[Section 14.5: A function that accepts named parameters](#section145)<br>
[Section 14.6: Return value from a function](#section146)<br>
[Section 14.7: The exit code of a function is the exit code of its last command](#section147)<br>


<a name="section141"></a>
### Section 14.1: Functions with arguments


```
#!/bin/bash
greet() {
    local name="$1"
    echo "Hello, $name"
}

greet "John Doe"
# running above script
$ bash helloJohn.sh
Hello, John Doe
```

1. Nếu bạn không sửa đổi đối số theo bất kỳ cách nào, thì không cần sao chép nó vào một biến `local` - chỉ cần echo
`"Hello, $1".`

2. Bạn có thể sử dụng $1, $2, $3, v.v. để truy cập các đối số bên trong hàm.

Lưu ý: đối với các đối số nhiều hơn 9 $10 sẽ không hoạt động (bash sẽ đọc nó là $10), bạn cần thực hiện ${10}, ${11}, v.v.

3. $@ tham chiếu đến tất cả các đối số của một hàm:

```
#!/bin/bash
foo() {
    echo "$@"
}

foo 1 2 3 # output => 1 2 3
```

Lưu ý: Trên thực tế, bạn nên luôn sử dụng dấu ngoặc kép xung quanh `"$@"`, như ở đây.

Việc bỏ qua các dấu ngoặc kép sẽ khiến shell mở rộng các ký tự đại diện (ngay cả khi người dùng đã trích dẫn cụ thể chúng để tránh điều đó) và thường dẫn đến hành vi không mong muốn và thậm chí có khả năng xảy ra các vấn đề bảo mật.

```
foo "string with spaces;" '$HOME' "*"
# output => string with spaces; $HOME *
```

4. đối với các đối số mặc định, hãy sử dụng `${1: -default_val}`. Ví dụ:

```
#!/bin/bash
foo() {
    local val=${1:-25}
    echo "$val"
}

foo # output => 25
foo 30 # output => 30
```

5. để yêu cầu một đối số, hãy sử dụng `${var:? Error message}`

```
foo() {
    local val=${1:?Must provide an argument}
    echo "$val"
}
```

<a name="section142"></a>
### Section 14.2: Simple Function


```
#!/bin/bash
# Define a function greet
greet ()
    {
    echo "Hello World!"
    }
# Call the function greet
greet
```

```
$ bash helloWorld.sh
Hello World!
```

```
$ source helloWorld.sh # or, more portably, ". helloWorld.sh"
$ greet
Hello World!
```

Bạn có thể xuất một hàm trong một số shell để nó tiếp xúc với các child processes.

```
bash -c 'greet' # fails
export -f greet # export function; note -f
bash -c 'greet' # success
```

<a name="section143"></a>
### Section 14.3: Handling flags and optional parameters

`getopts` có thể được sử dụng bên trong các hàm để viết các hàm phù hợp với cờ và các tham số tùy chọn. Điều này không có khó khăn đặc biệt nào nhưng người ta phải xử lý thích hợp các giá trị mà getopts khi xử lý vào.

Ví dụ, chúng tôi xác định một hàm failwith viết một thông báo trên `stderr` và thoát với code 1 hoặc code tùy ý
code được cung cấp dưới dạng tham số cho tùy chọn -x:

```
failwith()

{
    local OPTIND OPTION OPTARG status
	
    status=1
    OPTIND=1
	
    while getopts 'x:' OPTION; do
        case ${OPTION} in
            x) status="${OPTARG}";;
            *) 1>&2 printf 'failwith: %s: Unsupported option.\n' "${OPTION}";;
        esac
    done
    shift $(( OPTIND - 1 ))
    {
        printf 'Failure: '
        printf "$@"
        printf '\n'
    } 1>&2
    exit "${status}"
}
```


Chức năng này có thể được sử dụng như sau:

```
failwith '%s: File not found.' "${filename}"
failwith -x 70 'General internal error.'
```

Lưu ý rằng đối với printf, các biến không nên được sử dụng làm đối số đầu tiên. Nếu thông báo để in bao gồm nội dung của một biến, người ta nên sử dụng mã định nghĩa% s để in nó, như trong

```
failwith '%s' "${message}"
```

<a name="section144"></a>
### Section 14.4: Print the function definition

In ra nội dung của một funtion được khởi tạo.

```
getfunc() {
    declare -f "$@"
}

function func(){
    echo "I am a sample function"
}

funcd="$(getfunc func)"
getfunc func # or echo "$funcd"
```

Output:

````
func ()
{
    echo "I am a sample function"
}
```

<a name="section145"></a>
### Section 14.5: A function that accepts named parameters

```
foo() {
while [[ "$#" -gt 0 ]]

do
    case $1 in
        -f|--follow)
            local FOLLOW="following"
            ;;
        -t|--tail)
            local TAIL="tail=$2"
            ;;
    esac
    shift
done

echo "FOLLOW: $FOLLOW"
echo "TAIL: $TAIL"
}
```

```
foo -f
foo -t 10
foo -f --tail 10
foo --follow --tail 10
```

<a name="section146"></a>
### Section 14.6: Return value from a function


Câu lệnh return trong Bash không trả về giá trị như C-functions, thay vào đó nó thoát khỏi hàm với trạng thái trả về. Bạn có thể coi nó là trạng thái thoát của chức năng đó.

Nếu bạn muốn trả về một giá trị từ hàm thì hãy gửi giá trị đến stdout như sau:

```
fun() {
    local var="Sample value to be returned"
    echo "$var"
    #printf "%s\n" "$var"
}
```

Bây giờ nếu bạn chạy.

```
var="$(fun)"
```

output của function sẽ lưu ở `$var`

<a name="section147"></a>
### Section 14.7: The exit code of a function is the exit code of its last command

Hãy xem xét function ví dụ này để kiểm tra xem máy chủ có hoạt động hay không:

```
is_alive() {
    ping -c1 "$1" &> /dev/null
}
```

Hàm này sẽ gửi một ping đến máy chủ được chỉ định bởi tham số hàm đầu tiên. output và error output của ping đều được chuyển hướng đến `/dev/null`, vì vậy hàm sẽ không bao giờ xuất ra bất kỳ thứ gì. Nhưng lệnh ping sẽ có mã thoát 0 khi thành công và khác 0 khi thất bại. Vì đây là lệnh cuối cùng (và trong ví dụ này là lệnh duy nhất) của
function,code thoát của ping sẽ được sử dụng lại cho `exit code` của chính function.


Thực tế này rất hữu ích trong các câu lệnh điều kiện.

Ví dụ: nếu máy chủ graucho được thiết lập, hãy kết nối với máy chủ đó bằng ssh:

```
if is_alive graucho; then
    ssh graucho
fi
```

Một ví dụ khác: liên tục kiểm tra cho đến khi máy chủ graucho hoạt động, sau đó kết nối với nó bằng ssh:

```
while ! is_alive graucho; do
    sleep 5
done
ssh graucho
```


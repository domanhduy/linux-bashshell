# Chapter 15: Bash Parameter Expansion

$character giới thiệu mở rộng tham số, thay thế lệnh hoặc mở rộng số học. Tên hoặc ký hiệu tham số được mở rộng có thể được đặt trong dấu ngoặc nhọn, là tùy chọn nhưng dùng để bảo vệ biến được mở rộng khỏi các ký tự ngay sau nó mà có thể được hiểu là một phần của tên.

### Mục lục
[Section 15.1: Modifying the case of alphabetic characters](#section151)<br>
[Section 15.2: Length of parameter](#section152)<br>
[Section 15.3: Replace pattern in string](#section153)<br>
[Section 15.4: Substrings and subarrays](#section154)<br>
[Section 15.5: Delete a pattern from the beginning of a string](#section155)<br>
[Section 15.6: Parameter indirection](#section156)<br>
[Section 15.7: Parameter expansion and filename](#section157)<br>
[Section 15.8: Default value substitution](#section158)<br>
[Section 15.9: Delete a pattern from the end of a string](#section159)<br>
[Section 15.10: Munging during expansion](#section1510)<br>
[Section 15.11: Error if variable is empty or unset](#section1511)<br>

<a name="section151"></a>
### Section 15.1: Modifying the case of alphabetic characters

- To uppercase

```
$ v="hello"
# Just the first character
$ printf '%s\n' "${v^}"
Hello
# All characters
$ printf '%s\n' "${v^^}"
HELLO
# Alternative
$ v="hello world"
$ declare -u string="$v"
$ echo "$string"
HELLO WORLD
```

- To lowercase

```
$ v="BYE"
# Just the first character
$ printf '%s\n' "${v,}"
bYE
# All characters
$ printf '%s\n' "${v,,}"
bye
# Alternative
$ v="HELLO WORLD"
$ declare -l string="$v"
$ echo "$string"
hello world
```

- Toggle Case

```
$ v="Hello World"
# All chars
$ echo "${v~~}"
hELLO wORLD
$ echo "${v~}"
# Just the first char
hello World
```

<a name="section152"></a>
### Section 15.2: Length of parameter

```
# Length of a string
$ var='12345'
$ echo "${#var}
```

Lưu ý rằng đó là độ dài tính theo số ký tự không nhất thiết phải bằng số byte (như trong UTF-8 trong đó hầu hết các ký tự được mã hóa bằng nhiều hơn một byte), cũng không phải số `glyphs/grapheme` (một số kết hợp các ký tự), cũng không nhất thiết phải giống với chiều rộng hiển thị.

```
# Number of array elements
$ myarr=(1 2 3)
$ echo "${#myarr[@]}"
3

# Works for positional parameters as well
$ set -- 1 2 3 4
$ echo "${#@}"
4

# But more commonly (and portably to other shells), one would use
$ echo "$#"
4
```

### Section 15.3: Replace pattern in string

First match:

```
$ a='I am a string'
$ echo "${a/a/A}"
I Am a string
```

All matches:

```
$ echo "${a//a/A}"
I Am A string
```

Match at the beginning:

```
$ echo "${a/#I/y}"
y am a string
```

Match at the end:

```
$ echo "${a/%g/N}"
I am a strinN
```

Replace a pattern with nothing:

```
$ echo "${a/g/}"
I am a strin
```

Add prefix to array items:

```
$ A=(hello world)
$ echo "${A[@]/#/R}"
Rhello Rworld
```

<a name="section154"></a>
### Section 15.4: Substrings and subarrays

```
var='0123456789abcdef'
# Define a zero-based offset
$ printf '%s\n' "${var:3}"
3456789abcdef

# Offset and length of substring
$ printf '%s\n' "${var:3:4}"
3456


# Negative length counts from the end of the string
$ printf '%s\n' "${var:3:-5}"
3456789a


# Negative offset counts from the end
# Needs a space to avoid confusion with ${var:-6}
$ printf '%s\n' "${var: -6}"
abcdef

# Alternative: parentheses
$ printf '%s\n' "${var:(-6)}"
abcdef

# Negative offset and negative length
$ printf '%s\n' "${var: -6:-5}"
a
```

Các mở rộng tương tự cũng áp dụng nếu tham số là tham số vị trí hoặc phần tử của mảng được chỉ định con:

```
# Set positional parameter $1
set -- 0123456789abcdef

# Define offset
$ printf '%s\n' "${1:5}"
56789abcdef

# Assign to array element
myarr[0]='0123456789abcdef'

# Define offset and length
$ printf '%s\n' "${myarr[0]:7:3}"
789
```

Mở rộng tương tự áp dụng cho các tham số vị trí, trong đó hiệu số là một dựa trên:

```
# Set positional parameters $1, $2, ...
$ set -- 1 2 3 4 5 6 7 8 9 0 a b c d e f
# Define an offset (beware $0 (not a positional parameter)
# is being considered here as well)
$ printf '%s\n' "${@:10}"
0
a
b
c
d
e
f



# Define an offset and a length
$ printf '%s\n' "${@:10:3}"
0
a
b

# No negative lengths allowed for positional parameters
$ printf '%s\n' "${@:10:-2}"
bash: -2: substring expression < 0

# Negative offset counts from the end
# Needs a space to avoid confusion with ${@:-10:2}
$ printf '%s\n' "${@: -10:2}"
7
8

# ${@:0} is $0 which is not otherwise a positional parameters or part
# of $@
$ printf '%s\n' "${@:0:2}"
/usr/bin/bash
1
```

Mở rộng chuỗi con có thể được sử dụng với các mảng được lập chỉ mục:

```
# Create array (zero-based indices)
$ myarr=(0 1 2 3 4 5 6 7 8 9 a b c d e f)

# Elements with index 5 and higher
$ printf '%s\n' "${myarr[@]:12}"

c
d
e
f

# 3 elements, starting with index 5
$ printf '%s\n' "${myarr[@]:5:3}"

5
6
7

# The last element of the array
$ printf '%s\n' "${myarr[@]: -1}"
f
```

<a name="section155"></a>
### Section 15.5: Delete a pattern from the beginning of a string

Shortest match:

```
$ a='I am a string'
$ echo "${a#*a}"
m a string
```

Longest match:

```
$ echo "${a##*a}"
string
```

<a name="section156"></a>
### Section 15.6: Parameter indirection

Bash indirection cho phép lấy giá trị của một biến có tên được chứa trong một biến khác. 

```
$ red="the color red"
$ green="the color green"

$ color=red
$ echo "${!color}"
the color red
$ color=green
$ echo "${!color}"
the color green
```

Một số ví dụ khác chứng minh việc sử dụng mở rộng gián tiếp:

```
$ foo=10
$ x=foo
$ echo ${x} #Classic variable print
foo


$ foo=10
$ x=foo
$ echo ${!x} #Indirect expansion
10
```

<a name="section157"></a>
### Section 15.7: Parameter expansion and filename

Bạn có thể sử dụng Parameter expansion để mô phỏng các hoạt động xử lý tên tệp chung như `Basename` và `Dirname`.

Ví dụ:

```
FILENAME="/tmp/example/myfile.txt"
```

Để mô phỏng `DirName` và trả về tên thư mục của đường dẫn tệp:

```
echo "${FILENAME%/*}"
#Out: /tmp/example
```

Để mô phỏng `Basename` `$FileName` và trả về tên tệp của đường dẫn tệp:

```
echo "${FILENAME##*/}"
#Out: myfile.txt
```

Để mô phỏng `Basename` `$FileName.txt` và trả về tên tệp mà không có `.txt`. extension:

```
BASENAME="${FILENAME##*/}"
echo "${BASENAME%%.txt}"
#Out: myfile
```

<a name="section158"></a>
### Section 15.8: Default value substitution (thay thế giá trị mặc định)

```
${parameter:-word}
```

Nếu tham số không được đặt unset hoặc null, việc mở rộng từ được thay thế. Nếu không, giá trị của tham số được thay thế. 

```
$ unset var
$ echo "${var:-XX}" # Parameter is unset -> expansion XX occurs
XX
$ var="" # Parameter is null -> expansion XX occurs
$ echo "${var:-XX}"
XX
$ var=23 # Parameter is not null -> original expansion occurs
$ echo "${var:-XX}"
23
```


```
${parameter:=word}
```

Nếu tham số không được đặt unset hoặc null, việc mở rộng Word được gán cho tham số. Giá trị của tham số là
sau đó thay thế. Các tham số vị trí và các thông số đặc biệt có thể không được chỉ định theo cách này

```
$ unset var
$ echo "${var:=XX}" # Parameter is unset -> word is assigned to XX
XX
$ echo "$var"
XX
$ var="" # Parameter is null -> word is assigned to XX
$ echo "${var:=XX}"
XX
$ echo "$var"
XX
$ var=23 # Parameter is not null -> no assignment occurs
$ echo "${var:=XX}"
23
$ echo "$var"
23
```

<a name="section159"></a>
### Section 15.9: Delete a pattern from the end of a string

Shortest match:

```
$ a='I am a string'
$ echo "${a%a*}"
I am
```

Longest match:

```
$ echo "${a%%a*}"
I
```

<a name="section1510"></a>
### Section 15.10: Munging during expansion

Các biến không nhất thiết phải mở rộng sang giá trị của chúng - `substrings` có thể được trích xuất trong quá trình mở rộng, có thể hữu ích để trích xuất phần mở rộng tệp hoặc các phần của đường dẫn. characters global giữ ý nghĩa thông thường của họ, vì vậy`.*` Đề cập đến một dấu chấm theo nghĩa đen, theo sau là bất kỳ chuỗi ký tự nào; Đó không phải là một biểu thức chính quy.

```
$ v=foo-bar-baz
$ echo ${v%%-*}
foo
$ echo ${v%-*}
foo-bar
$ echo ${v##*-}
baz
$ echo ${v#*-}
bar-baz
```

Cũng có thể mở rộng một biến bằng cách sử dụng giá trị mặc định - giả sử tôi muốn gọi trình chỉnh sửa của người dùng, nhưng nếu họ không. Đặt một người tôi muốn cung cấp cho họ vim.

```
$ EDITOR=nano
$ ${EDITOR:-vim} /tmp/some_file
# opens nano
$ unset EDITOR
$ $ ${EDITOR:-vim} /tmp/some_file
# opens vim
```

Có hai cách khác nhau để thực hiện mở rộng này, khác nhau về biến có liên quan là empty hoặc
unset. Sử dụng `:-` sẽ sử dụng mặc định nếu biến không được đặt hoặc trống, trong khi `-` chỉ sử dụng mặc định nếu. Biến không được đặt, nhưng sẽ sử dụng biến nếu nó được đặt thành chuỗi trống:

```
$ a="set"
$ b=""
$ unset c
$ echo ${a:-default_a} ${b:-default_b} ${c:-default_c}
set default_b default_c
$ echo ${a-default_a} ${b-default_b} ${c-default_c}
set default_c
```

Tương tự như mặc định, các lựa chọn thay thế có thể được đưa ra; Trong trường hợp một mặc định được sử dụng nếu một biến cụ thể không khả dụng, một thay thế được sử dụng nếu biến có sẵn.

```
$ a="set"
$ b=""
$ echo ${a:+alternative_a} ${b:+alternative_b}
alternative_a
```

<a name="section1511"></a>
### Section 15.11: Error if variable is empty or unset

Tương tự như thay thế giá trị mặc định, nhưng thay vì thay thế một giá trị mặc định, nó đã lỗi với thông báo lỗi được cung cấp. Các form là `${varname?Errmsg}` và `${varname:?Errmsg}`. Biểu mẫu với `:` sẽ lỗi nếu biến của chúng tôi không được đặt hoặc trống, trong khi biểu mẫu mà không có lỗi sẽ xảy ra nếu biến không được đặt. Nếu bị lỗi bị ném, errmsg là đầu ra và mã thoát được đặt thành 1.

```
#!/bin/bash
FOO=
# ./script.sh: line 4: FOO: EMPTY
echo "FOO is ${FOO:?EMPTY}"
# FOO is
echo "FOO is ${FOO?UNSET}"
# ./script.sh: line 8: BAR: EMPTY
echo "BAR is ${BAR:?EMPTY}"
# ./script.sh: line 10: BAR: UNSET
echo "BAR is ${BAR?UNSET}"
```




































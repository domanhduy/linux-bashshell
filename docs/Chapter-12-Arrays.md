# Chapter 12 Arrays


### Mục lục
[Section 12.1: Array Assignments](#section121)<br>
[Section 12.2: Accessing Array Elements](#section122)<br>
[Section 12.3: Array Modification](#section123)<br>
[Section 12.4: Array Iteration)]<br>(#section124)
[Section 12.5: Array Length](#section125)<br>
[Section 12.6: Associative Arrays - Liên kết mảng](#section126)<br>
[Section 12.7: Looping through an array](#section127)<br>
[Section 12.8: Destroy, Delete, or Unset an Array](#section128)<br>
[Section 12.9: Array from string](#section129)<br>
[Section 12.10: List of initialized indexes](#section1210)<br>
[Section 12.11: Reading an entire file into an array](#section1211)<br>
[Section 12.12: Array insert function](#section1212)<br>


<a name="section121"></a>
### Section 12.1: Array Assignments

**List Assignment**

Nếu bạn đã quen với Perl, C hoặc Java, bạn có thể nghĩ rằng Bash sẽ sử dụng dấu phẩy để phân tách các phần tử mảng,
Tuy nhiên, đây không phải là trường hợp; thay vào đó, Bash sử dụng dấu cách:

```
# Array in Perl
my @array = (1, 2, 3, 4);
# Array in Bash
array=(1 2 3 4)
```

Tạo một mảng với các phần tử mới:

```
array=('first element' 'second element' 'third element')
```

**Subscript Assignment**

Tạo một mảng với các chỉ số phần tử rõ ràng:

```
array=([3]='fourth element' [4]='fifth element')
```

**Assignment by index**

Chỉ định theo chỉ mục

```
array[0]='first element'
array[1]='second element'
```

**Assignment by name (associative array)**

Chỉ định theo tên (mảng kết hợp)

```
declare -A array
array[first]='First element'
array[second]='Second element'
```

**Dynamic Assignment**

Tạo một mảng từ đầu ra của lệnh khác, ví dụ: sử dụng seq để nhận phạm vi từ 1 đến 10:

```
array=(`seq 1 10`)
```

Phép gán từ các đối số đầu vào của tập lệnh:

```
array=("$@")
```

**Assignment within loops:**

```
while read -r; do
    #array+=("$REPLY") # Array append
    array[$i]="$REPLY" # Assignment by index
    let i++ # Increment index
done < <(seq 1 10) # command substitution

echo ${array[@]} # output: 1 2 3 4 5 6 7 8 9 10
```

trong đó $ REPLY luôn là đầu vào hiện tại

<a name="section122"></a>
### Section 12.2: Accessing Array Elements

- In phần tử ở chỉ mục 0

```
echo "${array[0]}"
```

- In phần tử cuối cùng bằng cú pháp expansion syntax

```
echo "${arr[@]: -1 }
```

- In phần tử cuối cùng bằng cú pháp subscript syntax

```
echo "${array[-1]}"
```

- In tất cả các phần tử, mỗi phần tử được trích dẫn riêng biệt

```
echo "${array[@]}"
```

- In tất cả các phần tử dưới dạng một chuỗi được trích dẫn duy nhất

```
echo "${array[*]}"
```

- In tất cả các phần tử từ chỉ mục 1, mỗi phần tử được trích dẫn riêng biệt

```
echo "${array[@]:1}
```

- In 3 phần tử từ chỉ mục 1, mỗi phần tử được trích dẫn riêng biệt

```
echo "${array[@]:1:3}"
```

**String Operations**

Nếu tham chiếu đến một phần tử, các phép toán chuỗi được phép:

```
array=(zero one two)
echo "${array[0]:0:3}" # gives out zer (chars at position 0, 1 and 2 in the string zero)
echo "${array[0]:1:3}" # gives out ero (chars at position 1, 2 and 3 in the string zero)
```

vì vậy `${array[$i]:N:M}` đưa ra một chuỗi từ vị trí thứ N (bắt đầu từ 0) trong chuỗi `${array[$i]}` với M
các ký tự sau.

<a name="section123"></a>
### Section 12.3: Array Modification

**Change Index**

Khởi tạo hoặc cập nhật một phần tử cụ thể trong mảng

```
array[10]="elevenths element" # because it's starting with 0
```

**Append**

Sửa đổi mảng, thêm phần tử vào cuối nếu không có chỉ số con nào được chỉ định.

```
array+=('fourth element' 'fifth element')
```

Thay thế toàn bộ mảng bằng một danh sách tham số mới.

```
array=("${array[@]}" "fourth element" "fifth element")
```

Thêm một phần tử vào đầu:

```
array=("new element" "${array[@]}")
```

**Insert**

Chèn một phần tử tại một chỉ mục nhất định:

```
arr=(a b c d)
# insert an element at index 2
i=2
arr=("${arr[@]:0:$i}" 'new' "${arr[@]:$i}")
echo "${arr[2]}" #output: new
```

**Delete**

Xóa chỉ mục mảng bằng cách sử dụng `unset`:

```
arr=(a b c)
echo "${arr[@]}" # outputs: a b c
echo "${!arr[@]}" # outputs: 0 1 2
unset -v 'arr[1]'
echo "${arr[@]}" # outputs: a c
echo "${!arr[@]}" # outputs: 0 2
```

**Merge**

```
array3=("${array1[@]}" "${array2[@]}")
```

Điều này cũng hoạt động cho các mảng rời rạc.

**Re-indexing an array**

Điều này có thể hữu ích nếu các phần tử đã bị xóa khỏi một mảng hoặc nếu bạn không chắc liệu có khoảng trống trong mảng hay không. Để tạo lại các chỉ số mà không có khoảng trống:

```
array=("${array[@]}")
```

<a name="section124"></a>
### Section 12.4: Array Iteration (Mảng lặp)

Lặp lại mảng có hai loại, foreach và for-loop truyền thống:

```
a=(1 2 3 4)
# foreach loop
for y in "${a[@]}"; do
    # act on $y
    echo "$y"
done


# classic for-loop
for ((idx=0; idx < ${#a[@]}; ++idx)); do
    # act on ${a[$idx]}
    echo "${a[$idx]}"
done
```

Bạn cũng có thể lặp lại đầu ra của một lệnh:

```
a=($(tr ',' ' ' <<<"a,b,c,d")) # tr can transform one character to another
for y in "${a[@]}"; do
    echo "$y"
done
```

<a name="section125"></a>
### Section 12.5: Array Length

`${#array[@]}` cho biết độ dài của mảng `${array[@]}`:

```
array=('first element' 'second element' 'third element')
echo "${#array[@]}" # gives out a length of 3
```

Điều này cũng hoạt động với các Chuỗi trong các phần tử đơn lẻ:

```
echo "${#array[0]}" # gives out the lenght of the string at element 0: 13
```

<a name="section126"></a>
### Section 12.6: Associative Arrays - Liên kết mảng

**Declare an associative array - Khai báo một mảng kết hợp**

```
declare -A aa
```

Việc khai báo một mảng kết hợp trước khi khởi tạo hoặc sử dụng là bắt buộc.

**Initialize elements**

Bạn có thể khởi tạo từng phần tử một như sau:

```
aa[hello]=world
aa[ab]=cd
aa["key with space"]="hello world"
```

Bạn cũng có thể khởi tạo toàn bộ một mảng kết hợp trong một câu lệnh:

```
aa=([hello]=world [ab]=cd ["key with space"]="hello world")
```

**Access an associative array element**

Truy cập một phần tử mảng kết hợp

```
echo ${aa[hello]}
# Out: world

echo "${!aa[@]}"
#Out: hello ab key with space
```

**Listing associative array values**

Liệt kê các giá trị mảng kết hợp

```
echo "${aa[@]}"
#Out: world cd hello world
```

**Iterate over associative array keys and values**

Lặp lại các khóa và giá trị mảng kết hợp

```
for key in "${!aa[@]}"; do
    echo "Key: ${key}"
    echo "Value: ${array[$key]}"
done

# Out:
# Key: hello
# Value: world
# Key: ab
# Value: cd
# Key: key with space
# Value: hello world
```

**Count associative array elements**

Đếm phần tử mảng kết hợp

```
echo "${#aa[@]}"
# Out: 3
```

<a name="section127"></a>
### Section 12.7: Looping through an array

Our example array:

```
arr=(a b c d e f)
```

Using a for..in loop:

```
for i in "${arr[@]}"; do
    echo "$i"
done
```

Using C-style for loop:

```
for ((i=0;i<${#arr[@]};i++)); do
    echo "${arr[$i]}"
done
```

Using while loop:

```
i=0
while [ $i -lt ${#arr[@]} ]; do
    echo "${arr[$i]}"
    i=$((i + 1))
done
```

Using while loop with numerical conditional:

```
i=0
while (( $i < ${#arr[@]} )); do
    echo "${arr[$i]}"
    ((i++))
done
```

Using an until loop:

```
i=0
until [ $i -ge ${#arr[@]} ]; do
    echo "${arr[$i]}"
    i=$((i + 1))
done
```

Using an until loop with numerical conditional:

```
i=0
until (( $i >= ${#arr[@]} )); do
    echo "${arr[$i]}"
    ((i++))
done
```

<a name="section128"></a>
### Section 12.8: Destroy, Delete, or Unset an Array

To destroy, delete, or unset an array:

```
unset array
```

To destroy, delete, or unset a single array element:

```
unset array[10]
```

<a name="section129"></a>
### Section 12.9: Array from string

```
stringVar="Apple Orange Banana Mango"
arrayVar=(${stringVar// / })
```

Mỗi khoảng trắng trong chuỗi biểu thị một mục mới trong mảng kết quả.

```
echo ${arrayVar[0]} # will print Apple
echo ${arrayVar[3]} # will print Mango
```

Tương tự, các ký tự khác có thể được sử dụng cho dấu phân cách.

```
stringVar="Apple+Orange+Banana+Mango"
arrayVar=(${stringVar//+/ })
echo ${arrayVar[0]} # will print Apple
echo ${arrayVar[2]} # will print Banana
```

<a name="section1210"></a>
### Section 12.10: List of initialized indexes

Danh sách các chỉ mục được khởi tạo

Lấy danh sách các chỉ mục được vô hiệu hóa trong một mảng

```
$ arr[2]='second'
$ arr[10]='tenth'
$ arr[25]='twenty five'
$ echo ${!arr[@]}
2 10 25
```

<a name="section1211"></a>
### Section 12.11: Reading an entire file into an array

Reading in a single step:

```
IFS=$'\n' read -r -a arr < file
```

Reading in a loop:

```
arr=()
while IFS= read -r line; do
    arr+=("$line")
done
```

Using mapfile or readarray (which are synonymous):

```
mapfile -t arr < file
readarray -t arr < file
```

<a name="section1212"></a>
### Section 12.12: Array insert function

Hàm này sẽ chèn một phần tử vào một mảng tại một chỉ mục nhất định:

```
insert(){
    h='
################## insert ########################
# Usage:
# insert arr_name index element
#
# Parameters:
# arr_name : Name of the array variable
# index : Index to insert at
# element : Element to insert
##################################################
'
    [[ $1 = -h ]] && { echo "$h" >/dev/stderr; return 1; }
    declare -n __arr__=$1 # reference to the array variable
    i=$2                  # index to insert at
    el="$3"               # element to insert
    # handle errors
    [[ ! "$i" =~ ^[0-9]+$ ]] && { echo "E: insert: index must be a valid integer" >/dev/stderr;
return 1; }
    (( $1 < 0 )) && { echo "E: insert: index can not be negative" >/dev/stderr; return 1; }
    # Now insert $el at $i
    __arr__=("${__arr__[@]:0:$i}" "$el" "${__arr__[@]:$i}")
}
```

Usage:

```
insert array_variable_name index element
```

```
arr=(a b c d)
echo "${arr[2]}" # output: c
# Now call the insert function and pass the array variable name,
# index to insert at
# and the element to insert
insert arr 2 'New Element'
# 'New Element' was inserted at index 2 in arr, now print them
echo "${arr[2]}" # output: New Element
echo "${arr[3]}" # output: c
```



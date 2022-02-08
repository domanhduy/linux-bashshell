# Chapter 18: Using sort

### Mục lục
[Section 18.1: Sort command output](#section181)<br>
[Section 18.2: Make output unique](#section182)<br>
[Section 18.3: Numeric sort](#section183)<br>
[Section 18.4: Sort by keys](#section183)<br>


```
Option Meaning
-u      Make each lines of output unique (Làm cho mỗi dòng đầu ra là duy nhất)
sort is a Unix command to order data in file(s) in a sequence. (sắp xếp là một lệnh Unix để sắp xếp dữ liệu trong (các) tệp theo một trình tự.)
```

<a name="section181"></a>
### Section 18.1: Sort command output

Lệnh `sort` được sử dụng để sắp xếp một danh sách các dòng.

Input from a file

```
sort file.txt
```

Input from a command

Bạn có thể sắp xếp bất kỳ lệnh đầu ra nào. Trong ví dụ, danh sách tệp theo sau một mẫu.

```
find * -name pattern | sort
```

<a name="section182"></a>
### Section 18.2: Make output unique

Nếu mỗi dòng của đầu ra cần phải là duy nhất, hãy thêm tùy chọn -u.

Để hiển thị chủ sở hữu của các tệp trong thư mục


```
ls -l | awk '{print $3}' | sort -u
```

<a name="section183"></a>
### Section 18.3: Numeric sort

Ví dụ:

```
test>>cat file
10.Gryffindor
4.Hogwarts
2.Harry
3.Dumbledore
1.The sorting hat
```

Để sắp xếp tệp này theo số, hãy sử dụng tùy chọn sắp xếp với -n:

```
test>>sort -n file
```

```
1.The sorting hat
2.Harry
3.Dumbledore
4.Hogwarts
10.Gryffindor
```

Đảo ngược thứ tự sắp xếp: Để đảo ngược thứ tự sắp xếp, hãy sử dụng tùy chọn `-r`

Để đảo ngược thứ tự sắp xếp của tệp ở trên, hãy sử dụng:

```
sort -rn file
```

```
10.Gryffindor
4.Hogwarts
3.Dumbledore
2.Harry
1.The sorting hat
```

<a name="section184"></a>
### Section 18.4: Sort by keys

```
test>>cat Hogwarts
Harry       Malfoy    Rowena    Helga
Gryffindor  Slytherin Ravenclaw Hufflepuff
Hermione    Goyle     Lockhart  Tonks
Ron         Snape     Olivander Newt
Ron         Goyle     Flitwick  Sprout
```

Để sắp xếp tệp này bằng cách sử dụng một cột làm khóa, hãy sử dụng tùy chọn k:

```
test>>sort -k 2 Hogwarts
```

Thao tác này sẽ sắp xếp tệp với cột 2 làm khóa:

```
Ron         Goyle     Flitwick   Sprout
Hermione    Goyle     Lockhart   Tonks
Harry       Malfoy    Rowena     Helga
Gryffindor  Slytherin Ravenclaw  Hufflepuff
Ron         Snape     Olivander  Newt
```

Bây giờ nếu chúng ta phải sắp xếp tệp bằng khóa phụ cùng với việc sử dụng khóa chính:

```
sort -k 2,2 -k 1,1 Hogwarts
```

Điều này đầu tiên sẽ sắp xếp tệp với cột 2 làm khóa chính, sau đó sắp xếp tệp với cột 1 làm khóa phụ:

```
Hermione   Goyle      Lockhart  Tonks
Ron        Goyle      Flitwick  Sprout
Harry      Malfoy     Rowena    Helga
Gryffindor Slytherin  Ravenclaw Hufflepuff
Ron        Snape      Olivander Newt
```

Nếu chúng ta cần sắp xếp một tệp có nhiều hơn 1 khóa, thì đối với mọi tùy chọn -k, chúng ta cần chỉ định nơi sắp xếp kết thúc. Vì vậy -k1,1 có nghĩa là bắt đầu sắp xếp ở cột đầu tiên và kết thúc sắp xếp ở cột đầu tiên.


```
-t option
```

Trong ví dụ trước, tệp này có tab - delimeter  mặc định. Trong trường hợp sắp xếp một tệp có mã phân đoạn không mặc định, chúng ta cần tùy chọn -t để chỉ định mẫu phân đoạn. Giả sử chúng ta có tệp như sau:


```
test>>cat file

5.|Gryffindor
4.|Hogwarts
2.|Harry
3.|Dumbledore
1.|The sorting hat
```

Để sắp xếp tệp này theo cột thứ hai, hãy sử dụng:

```
test>>sort -t "|" -k 2 file
```

Thao tác này sẽ sắp xếp tệp như dưới đây:

```
3.|Dumbledore
5.|Gryffindor
2.|Harry
4.|Hogwarts
1.|The sorting hat
```


# Chapter 16: Copying (cp)



### Mục lục
[Section 16.1: Copy a single file](#section161)<br>
[Section 16.2: Copy folders](#section162)<br>

```
Option Description
-a,-archive Combines the d, p and r options
-b, -backup Before removal, makes a backup
-d, --no-deference Preserves links
-f, --force Remove existing destinations without prompting user
-i, --interactive Show prompt before overwriting
-l, --link Instead of copying, link files instead
-p, --preserve Preserve file attributes when possible
-R, --recursive Recursively copy directories
```

<a name="section161"></a>
### Section 16.1: Copy a single file

Copy `foo.txt` from `/path/to/source/` to `/path/to/target/folder/`

```
cp /path/to/source/foo.txt /path/to/target/folder/
```

Copy `foo.txt` from `/path/to/source/` to `/path/to/target/folder/` into a file called `bar.txt`

```
cp /path/to/source/foo.txt /path/to/target/folder/bar.txt
```

<a name="section162"></a>
### Section 16.2: Copy folders

copy folder foo into folder bar

```
cp -r /path/to/foo /path/to/bar
```

Nếu thư mục `bar` tồn tại trước khi chạy lệnh, thì `foo` và nội dung của nó sẽ được sao chép vào thư mục `bar`.
Tuy nhiên, nếu `bar` không tồn tại trước khi ra lệnh, thì thư mục `bar` sẽ được tạo và nội dung của `foo` sẽ được đặt vào `bar`.


# Chapter 4: Listing Files

```
Option Description
-a, --all List all entries including ones that start with a dot
-A, --almost-all List all entries excluding . and ..
-c Sort files by change time
-d, --directory List directory entries
-h, --human-readable Show sizes in human readable format (i.e. K, M)
-H Same as above only with powers of 1000 instead of 1024
-l Show contents in long-listing format
-o Long -listing format without group info
-r, --reverse Show contents in reverse order
-s, --size Print size of each file in blocks
-S Sort by file size
--sort=WORD Sort contents by a word. (i.e size, version, status)
-t Sort by modification time
-u Sort by last access time
-v Sort by version
-1 List one file per line
```

### Mục lục
[Section 4.1: List Files in a Long Listing Format](#section41)<br>
[Section 4.2: List the Ten Most Recently Modified Files](#section42)<br>
[Section 4.3: List All Files Including Dotfiles](#section43)<br>
[Section 4.4: List Files Without Using `ls`](#section44)<br>
[Section 4.5: List Files](#section45)<br>
[Section 4.6: List Files in a Tree-Like Format](#section46)<br>
[Section 4.7: List Files Sorted by Size](#section47)<br>

<a name="section41"></a>
## Section 4.1: List Files in a Long Listing Format

- `ls` command sử dụng với option `-l` để in ra một thư mục cụ thể ở dạng danh sách dài. Nếu không có thư mục nào được chỉ định thì sẽ theo mặc định nôi dung của thực mục hiện tại sẽ được liệt kê.

```
[root@vt ~]# ls -l /etc/
total 1440
drwxr-xr-x   4 root root     4096 Mar 29  2021 acpi
-rw-r--r--.  1 root root       44 Mar 29  2021 adjtime
-rw-r--r--.  1 root root     1529 Apr  1  2020 aliases
-rw-r--r--.  1 root root    12288 Mar 29  2021 aliases.db
drwxr-xr-x.  2 root root     4096 Dec 10 10:50 alternatives
-rw-------.  1 root root      541 Aug  9  2019 anacrontab
-rw-r--r--.  1 root root       55 Aug  8  2019 asound.conf
drwxr-x---.  3 root root     4096 Mar 29  2021 audisp
drwxr-x---.  3 root root     4096 Mar 29  2021 audit
drwxr-xr-x.  2 root root     4096 Dec 10 10:53 bash_completion.d
-rw-r--r--.  1 root root     2853 Apr  1  2020 bashrc
```

Kết quả hiện thỉ toàn bộ các mục có site của các mục và thể hiện 8 cột thông tin.

```
Column No. Example          Description
1.1        d                File type (see table below)
1.2        rwxr-xr-x        Permission string
2          3                Number of hard links
3          root             Owner name
4          root             Owner group
5          4096             File size in bytes
6          Apr 21 03:44     Modification time
7          acpi             File name
```

- File Type

```
Character        File Type
-                Regular file : Là một file bình thường có thể chứa dữ liệu text hoặc file thực thi binary.
b                Block special file: Dạng thiết bị lưu trữ ổ cứng, usb.
c                Character special file
C                High performance ("contiguous data") file
d                Directory
D                Door (special IPC file in Solaris 2.5+ only)
l                Symbolic link : Thể hiện một link giống sortcut trong windows.
M                Off-line ("migrated") file (Cray DMF)
n                Network special file (HP-UX)
p                FIFO (named pipe): Trao đổi thông tin giữa các process.
P                Port (special system file in Solaris 10+ only)
s                Socket: File trao đổi thông tin giữa các process.
?                Some other file type
```

<a name="section42"></a>
### Section 4.2: List the Ten Most Recently Modified Files

Phần sau sẽ liệt kê tối đa mười tệp được sửa đổi gần đây nhất trong thư mục hiện tại, sử dụng một danh sách dài
định dạng `(-l)` và được sắp xếp theo thời gian `(-t)`

[root@vt ~]# ls -lt
total 4
-rw-------. 1 root root 1374 Mar 29  2021 anaconda-ks.cfg
[root@viettel-smokeping ~]# ls -lt /etc/ | head
total 1440
drwxr-xr-x   2 root root     4096 Dec 16 14:36 smokeping
-rw-r--r--   1 root root       92 Dec 16 12:20 resolv.conf
drwxr-xr-x.  2 root root     4096 Dec 10 10:55 tmpfiles.d
-rw-r--r--   1 root root    30541 Dec 10 10:54 ld.so.cache
drwxr-xr-x   3 root root     4096 Dec 10 10:54 egl
drwxr-xr-x   3 root root     4096 Dec 10 10:54 glvnd
drwxr-xr-x   3 root root     4096 Dec 10 10:54 fonts
drwxr-xr-x.  6 root root     4096 Dec 10 10:54 sysconfig
drwxr-xr-x.  2 root root     4096 Dec 10 10:54 logrotate.d
[root@vt ~]#

<a name="section43"></a>
### Section 4.3: List All Files Including Dotfiles

Dotfile là một tệp có tên bắt đầu bằng `.`. Chúng thường được ẩn bởi ls và không được liệt kê trừ khi được yêu cầu.

```
[root@vt ~]# ls
anaconda-ks.cfg
[root@vt ~]# ls -a
.  ..  anaconda-ks.cfg  .bash_history  .bash_logout  .bash_profile  .bashrc  .cshrc  .pki  .ssh  .tcshrc
[root@vt ~]#

```

Tùy chọn `-A` hoặc `--almost-all` sẽ liệt kê tất cả các tệp, bao gồm các tệp dotfiles, nhưng không liệt kê ngụ ý `.` và `..` Lưu ý `.` là thư mục hiện tại và `..` là thư mục mẹ.

```
[root@vt ~]# ls -A
anaconda-ks.cfg  .bash_history  .bash_logout  .bash_profile  .bashrc  .cshrc  .pki  .ssh  .tcshrc
```

<a name="section44"></a>
### Section 4.4: List Files Without Using `ls`

```
# display the files and directories that are in the current directory
printf "%s\n" *
# display only the directories in the current directory
printf "%s\n" */
# display only (some) image files
printf "%s\n" *.{gif,jpg,png}
```


```
files=( * )
# iterate over them
for file in "${files[@]}"; do
echo "$file"
done
```

<a name="section45"></a>
### Section 4.5: List Files

Lệnh ls liệt kê nội dung của một thư mục được chỉ định, không bao gồm dotfiles. Nếu không có thư mục nào được chỉ định mặc định, nội dung của thư mục hiện tại sẽ được liệt kê.

Các tệp trong danh sách được sắp xếp theo thứ tự bảng chữ cái, theo mặc định và được căn chỉnh theo cột nếu chúng không vừa trên một dòng.

```
[root@vt ~]# ls
anaconda-ks.cfg  bash.sh  duydm  duy.txt
[root@vt ~]#
```

<a name="section46"></a>
### Section 4.6: List Files in a Tree-Like Format

Lệnh `tree` liệt kê nội dung của một thư mục được chỉ định ở định dạng giống như cây. Nếu không có thư mục nào được chỉ định thì, theo mặc định, nội dung của thư mục hiện tại được liệt kê.

```
[root@vt ~]# tree /root/
/root/
├── anaconda-ks.cfg
├── bash.sh
├── duydm
└── duy.txt

1 directory, 3 files
[root@vt ~]#
```

Sử dụng tùy chọn `-L` của lệnh `tree` để giới hạn độ sâu hiển thị và tùy chọn `-d` để chỉ liệt kê các thư mục.

```
[root@vt ~]# tree -L 1 -d /root/
/root/
└── duydm

1 directory
[root@vt ~]#
```

<a name="section47"></a>
### Section 4.7: List Files Sorted by Size

Tùy chọn `-S` của lệnh `ls` sắp xếp các tệp theo thứ tự giảm dần về kích thước tệp.


```
[root@vt ~]# ls -l -S
total 12
drwxr-xr-x  2 root root 4096 Dec 29 21:47 duydm
-rw-------. 1 root root 1374 Mar 29  2021 anaconda-ks.cfg
-rwxr-xr-x  1 root root  149 Dec 29 21:48 bash.sh
-rw-r--r--  1 root root    0 Dec 29 21:47 duy.txt
[root@viettel-smokeping ~]#
```

Khi được sử dụng với tùy chọn `-r`, thứ tự sắp xếp được đảo ngược.

```
[root@vt ~]# ls -l -r
total 12
-rw-r--r--  1 root root    0 Dec 29 21:47 duy.txt
drwxr-xr-x  2 root root 4096 Dec 29 21:47 duydm
-rwxr-xr-x  1 root root  149 Dec 29 21:48 bash.sh
-rw-------. 1 root root 1374 Mar 29  2021 anaconda-ks.cfg
[root@vt ~]#
```






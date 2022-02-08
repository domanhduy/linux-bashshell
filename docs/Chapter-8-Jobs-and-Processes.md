# Chapter 8: Jobs and Processes



### Mục lục
[Section 8.1: Job handling](#section81)<br>
[Section 8.2: Check which process running on specific port](#section82)<br>
[Section 8.3: Disowning background job](#section83)<br>
[Section 8.4: List Current Jobs](#section84)<br>
[Section 8.5: Finding information about a running process](#section85)<br>
[Section 8.6: List all processes](#section86)<br>


<a name="section81"></a>
### Section 8.1: Job handling

**Creating jobs**

Để tạo một jobs, chỉ cần thêm một `&` sau lệnh:

```
$ sleep 10 &
[1] 20024
```

Sử dụng `Ctrl + z` để biến một process thành một jobs.

```
[root@vt ~]# sleep 10
^Z[1]   Done                    sleep 10

[2]+  Stopped                 sleep 10
[root@vt ~]#
```


**Background và foreground một process**

Để đưa process lên Background sử dụng lệnh `fg` được sử dụng cùng với `%`

```
$ sleep 10 &
[1] 20024

$ fg %1
sleep 10
```

Bây giờ bạn có thể tương tác với process. Để đưa nó trở lại background , bạn có thể sử dụng lệnh `bg`. Do session cuối bị chiếm, trước tiên bạn cần dừng process bằng cách nhấn `Ctrl + z`.

```
$ sleep 10
^Z
[1]+ Stopped sleep 10
$ bg %1
[1]+ sleep 10 &
```

Do sự lười biếng của một số Lập trình viên, tất cả các commands này cũng hoạt động với một `%` duy nhất nếu chỉ có một process hoặc đối với process đầu tiên trong danh sách.

```
$ sleep 10 &
[1] 20024
$ fg % # to bring a process to foreground 'fg %' is also working.
sleep 10
```

Hoặc

```
$ % # laziness knows no boundaries, '%' is also working.
sleep 10
```

Ngoài ra, chỉ cần nhập `fg` hoặc `bg` mà không có bất kỳ đối số nào sẽ xử lý công việc cuối cùng:

```
$ sleep 20 &
$ sleep 10 &
$ fg
sleep 10
^C
$ fg
sleep 20
```

**Killing running jobs**

```
$ sleep 10 &
[1] 20024
$ kill %1
[1]+ Terminated sleep 10
```

The sleep process runs in the background with process id (pid) 20024 and job number 1. In order to reference the
process, you can use either the pid or the job number. If you use the job number, you must prefix it with %. The
default kill signal sent by kill is SIGTERM, which allows the target process to exit gracefully.
Some common kill signals are shown below. To see a full list, run kill -l

sleep process chạy trong background với process id (pid) 20024 và job number 1. Để tham chiếu bạn có thể sử dụng pid hoặc job number. Nếu bạn sử dựng job number bạn phải thêm `%` trước nó. Default kill signal được gửi bởi kill là `SIGTERM` cho phép tiến trình thoát ra một cách hợp lệ. Để xem danh sách đầy đủ, hãy chạy `kill -l`

```
Signal name Signal value  Effect
SIGHUP                    1 Hangup
SIGINT                    2 Interrupt from keyboard
SIGKILL                   9 Kill signal
SIGTERM                   15 Termination signal
```

**Start và kill specific processes**

```
Có lẽ cách dễ nhất để kill một tiến trình đang chạy là chọn nó thông qua name process như trong ví dụ sau bằng cách sử dụng lệnh pkill như:
```

```
pkill -f test.py
```

Hoặc sử dụng `fool-proof` để `pgrep` lấy `process-id`

```
kill $(pgrep -f 'python test.py')
```

Kết quả tương tự có thể đạt được bằng cách sử dụng grep over `ps -ef | grep name_of_process` sau đó kill được liên kết với `pid` kết quả (process id)

<a name="section82"></a>
### Section 8.2: Check which process running on specific port

```
lsof -i :8080
```

<a name="section83"></a>
### Section 8.3: Disowning background job

```
$ gzip extremelylargefile.txt &
$ bg
$ disown %1
```

Điều này cho phép một process chạy dài tiếp tục shell (terminal, ssh, etc) bị đóng.

<a name="section84"></a>
### Section 8.4: List Current Jobs

```
$ tail -f /var/log/syslog > log.txt
[1]+ Stopped tail -f /var/log/syslog > log.txt

$ sleep 10 &

$ jobs
[1]+ Stopped tail -f /var/log/syslog > log.txt
[2]- Running sleep 10 &
```

<a name="section85"></a>
### Section 8.5: Finding information about a running process

```
ps aux | grep <search-term> shows processes matching search-term
```


```
root@vt:~# ps aux | grep nginx
root 315 0.0 0.3 144392 1020 ? Ss May28 0:00 nginx: master process
/usr/sbin/nginx
www-data 5647 0.0 1.1 145124 3048 ? S Jul18 2:53 nginx: worker process
www-data 5648 0.0 0.1 144392 376 ? S Jul18 0:00 nginx: cache manager process
root 13134 0.0 0.3 4960 920 pts/0 S+ 14:33 0:00 grep --color=auto ng
```

Ở đây, cột thứ hai là process id. Ví dụ: nếu bạn muốn kill tiến trình nginx, bạn có thể sử dụng lệnh kill 5647. Bạn nên sử dụng lệnh kill với `SIGTERM` hơn là `SIGKILL`.

<a name="section86"></a>
### Section 8.6: List all processes

Có hai cách phổ biến để liệt kê tất cả các quy trình trên một hệ thống. Cả hai đều liệt kê tất cả các quy trình được chạy bởi tất cả người dùng, mặc dù chúng khác nhau về định dạng mà chúng xuất ra (lý do cho sự khác biệt là lịch sử).

```
ps -ef # lists all processes
ps aux # lists all processes in alternative format (BSD)
```

Điều này có thể được sử dụng để kiểm tra xem một ứng dụng nhất định có đang chạy hay không. Ví dụ: để kiểm tra xem máy chủ SSH (sshd) có đang chạy hay không:

```
[root@vt ~]# ps -ef | grep sshd
root      1220     1  0 Jan01 ?        00:00:00 /usr/sbin/sshd -D
root      7517  1220  0 Jan01 ?        00:00:00 sshd: root@pts/0
root      7519  1220  0 Jan01 ?        00:00:00 sshd: root@notty
root     19299  1220  0 16:47 ?        00:00:00 sshd: [accepted]
root     19317  7521  0 16:49 pts/0    00:00:00 grep --color=auto sshd
[root@vt ~]#
```
















































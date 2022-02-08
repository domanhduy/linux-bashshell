# Chapter 7: Aliasing

Alias Shell là một cách đơn giản để tạo các lệnh mới hoặc bọc các lệnh hiện có bằng mã của riêng. Chúng hơi trùng lặp với các hàm shell, tuy nhiên, chúng linh hoạt hơn và do đó thường được ưu tiên hơn.

```
[root@vt ~]# alias now='date'
[root@vt ~]# now
Sun Jan  2 09:25:05 +07 2022
```


### Mục lục
[Section 7.1: Bypass an alias](#section71)<br>
[Section 7.2: Create an Alias](#section72)<br>
[Section 7.3: Remove an alias](#section73)<br>
[Section 7.4: The BASH_ALIASES is an internal bash assoc array](#section74)<br>
[Section 7.5: Expand alias](#section75)<br>


<a name="section71"></a>
### Section 7.1: Bypass an alias

Đôi khi bạn có thể muốn bỏ qua alias tạm thời mà không cần disable nó. Ví dụ:

```
alias ls='ls --color=auto'
```

Và giả sử bạn muốn sử dụng lệnh ls mà không cần tắt bí danh. Bạn có một số tùy chọn:

```
Use the command builtin: command ls
Use the full path of the command: /bin/ls
Add a \ anywhere in the command name, for example: \ls, or l\s
Quote the command: "ls" or 'ls'
```

<a name="section72"></a>
### Section 7.2: Create an Alias

```
alias word='command'
```

Lệnh sẽ chạy, bất kỳ đối số nào được cung cấp cho alias chỉ được thêm vào đích của alias:

```
alias myAlias='some command --with --options'
myAlias foo bar baz
```

Sau đó, shell sẽ thực thi:

```
some command --with --options foo bar baz
```

Để bao gồm nhiều lệnh trong cùng một bí danh, bạn có thể xâu chuỗi chúng lại với nhau bằng &&. Ví dụ:


```
alias print_things='echo "foo" && echo "bar" && echo "baz"'
```

```
[root@vt ~]# alias print_things='echo "foo" && echo "bar" && echo "baz"'
[root@vt ~]# print_things
foo
bar
baz
[root@vt ~]#
```

<a name="section73"></a>
### Section 7.3: Remove an alias

Để xóa alias hiện có, hãy sử dụng:

```
unalias {alias_name}
```

```
# create an alias
$ alias now='date'
# preview the alias
$ now
Thu Jul 21 17:11:25 CEST 2016
# remove the alias
$ unalias now
# test if removed
$ now
-bash: now: command not found
```

```
[root@vt ~]# alias now='date'
[root@vt ~]# now
Sun Jan  2 09:30:32 +07 2022
[root@vt ~]# unalias now
[root@vt ~]# now
-bash: now: command not found
[root@vt ~]#
```

<a name="section74"></a>
### Section 7.4: The BASH_ALIASES is an internal bash assoc array

Alias được đặt tên là các phím tắt của lệnh, người ta có thể xác định và sử dụng trong các phiên bản bash tương tác. Chúng được tổ chức trong một mảng kết hợp có tên BASH_ALIASES. Để sử dụng `var` này trong một tập lệnh, nó phải được chạy trong một shell.

```
#!/bin/bash -li
# note the -li above! -l makes this behave like a login shell
# -i makes it behave like an interactive shell
#
# shopt -s expand_aliases will not work in most cases

alias now='date'
echo There are ${#BASH_ALIASES[*]} aliases defined.
for ali in "${!BASH_ALIASES[@]}"; do
printf "alias: %-10s triggers: %s\n" "$ali" "${BASH_ALIASES[$ali]}"
done
```

```
[root@vt ~]# ./bash.sh
There are 1 aliases defined.
alias: now        triggers: date
```

<a name="section75"></a>
### Section 7.5: Expand alias

Sử dụng thêm command `-flag`

<a name="section75"></a>
### Section 7.6: List all Aliases

```
alias -p
```

sẽ liệt kê tất cả các bí danh hiện tại

```
[root@vt ~]# alias -p
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias mv='mv -i'
alias now='date'
alias print_things='echo "foo" && echo "bar" && echo "baz"'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```













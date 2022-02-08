# Chapter 6: Grep

### Section 6.1: How to search a file for a pattern

Tìm kiểm trong một file với một mẫu (từ khóa).

Tìm kiếm một word `foo` in the file `bar` :

```
grep foo ~/Desktop/bar
```

Tìm kiếm all lines không chứa `foo` trong file bar :

```
grep –v foo ~/Desktop/bar
```

Để sử dụng, tìm tất cả các từ có chứa `foo` cuối cùng (WIldcard Expansion):

```
grep "*foo" ~/Desktop/bar
```




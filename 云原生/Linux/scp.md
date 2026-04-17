上传文件

`scp -P port filepath username@ip:TargetPath`

上传文件夹

`scp -r -P port filepath username@ip:TargetPath`

`-P` port：大写P，用于指定端口，port为指定端口号，若是默认端口则可省略

`-r` 表示filepath是一个文件夹，将整个文件夹上传到服务器

`filepath`：需上传的文件或文件夹的完整路径，若在文件所在文件夹打开终端执行此命令，则只需输入文件名即可

`username`：远程主机用户名

`ip`：远程主机IP地址

`TargetPath`：文件上传到服务器的目标目录


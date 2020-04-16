# 再学git
1. 安装git
2. 配置用户名和邮箱
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
3. 创建本地仓库
`$git init`
创建完成后该文件中会多出一个隐藏的.git文件，可以使用`$ls -ah`命令查看，若删除则会破坏git仓库。
4. 
- 将文件添加到本地版本库（暂存区）
	`$git add .`//全部添加
	`$git add readme.txt`//添加readme.txt文件
执行上面的命令，没有任何显示，这就对了，Unix的哲学是“没有消息就是好消息”，说明添加成功。
- 将文件提交到公共版本库（当前分支）
	`$git commit -m ""`
	提交后暂存区就没有内容了
5. `$git status`查看工作区状态，`$git diff`查看修改内容`$git diff HEAD `查看最新工作区与版本库区别

6. git版本操作
		`$git reset --hard HEAD^`回到上一个版本
		`$git log`查看历史记录，可以获取每个版本的版本号和commit注释等信息git时光机
		`$git reset --hard 【版本参数】`回到某个版本
		`$git reflog`查看每一次git操作命令，通过这个可以找到之前的操作的版本号，再回到未来的版本
	
7. 丢弃修改
		`$git checkout -- file`丢弃工作区的修改
		`$git reset HEAD`撤销暂存区的修改
	
	

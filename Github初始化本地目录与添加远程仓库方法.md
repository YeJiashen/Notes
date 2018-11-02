//创建readme.md文件
echo "# Notes" >> README.md
// 初始化本地目录, 可以是空文件夹也可以是含有文件的文件夹
git init
// add commit一套流程, 这里是提交到本地缓存区
git add README.md
git commit -m "first commit"
// 添加远程仓库, 这里为Notes.git这个仓库
git remote add origin git@github.com:YeJiashen/Notes.git
// 将修改的文件push到远程仓库
git push -u origin master

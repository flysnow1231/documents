# github新repo，本地的工程push
# 1. 进入你的项目目录
cd /aka-pledge

# 2. 初始化 git（如果还没初始化）
git init

# 3. 绑定远程仓库
git remote add origin git@github.com:flysnow1231/aka-pledge.git

# 4. 添加文件
git add .

# 5. 提交
git commit -m "init: first commit"

# 6. 推送到 main 分支
git branch -M main
git push -u origin main
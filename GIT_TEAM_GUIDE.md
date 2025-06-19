
# 团队Git使用规范

欢迎阅读本团队的Git协作流程指南，帮助大家保持代码同步，减少冲突，提高协作效率。

---

1. 仓库克隆

新成员或新设备第一次工作时，执行：
git clone https://github.com/用户名/仓库名.git

2. 日常开发流程
2a:拉取远端最新代码
git pull origin main

2b:修改代码或新增文件

2c:添加更改到暂存区
git add .

2d:提交更改
git commit -m "简洁描述本次更改"

2f:推送到远端
git push origin main

3. 冲突处理
拉取代码时可能出现冲突，请根据Git提示手动修改冲突部分

修改完成后执行：

git add .
git commit -m "解决冲突"
git push origin main

4. 分支管理建议
**主分支（main）**保持稳定，只合并经过测试的代码

新功能、新需求建议创建新分支开发：

git checkout -b feature/功能描述
开发完成后合并回主分支，建议通过Pull Request流程完成代码审查


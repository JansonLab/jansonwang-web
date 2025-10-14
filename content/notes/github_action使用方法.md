+++
title = "Github-Action使用方法"
author = ["ubuntu"]
date = 2025-10-14T09:54:00+08:00
draft = false
+++

## Github Action 使用方法 {#github-action-使用方法}

Quartz v4 的模板在 .github/workflows/deploy.yml 文件中已经为你准备好了部署脚本。你通常不需要修改它。

-   前往仓库设置: 在你的 GitHub 仓库页面，点击 "Settings" (设置)。
-   进入 Pages 设置: 在左侧菜单中，选择 "Pages"。

-   配置发布源:
    -   在 "Build and deployment" (构建和部署) 下，找到 "Source" (源)。
    -   选择 "GitHub Actions"。


### 触发部署 {#触发部署}

现在，你只需要对你的项目做一次新的 git push，就可以触发自动部署。

```shell
# 可以随便做一点小修改，比如修改一个md文件
git add .
git commit -m "Trigger deployment"
git push
```

推送后，你可以进入 GitHub 仓库的 "Actions" 标签页，会看到一个正在运行的 workflow。当它成功完成后（显示绿色对勾），你的网站就发布成功了！

等待几分钟，然后访问你的网站地址：

-   <https://你的用户名.github.io/>

-   或者 <https://你的用户名.github.io/%E4%BD%A0%E7%9A%84%E4%BB%93%E5%BA%93%E5%90%8D/>

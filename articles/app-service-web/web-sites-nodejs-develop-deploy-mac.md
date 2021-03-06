---
title: "在 Azure App Service 中创建 Node.js Web 应用 | Microsoft Docs"
description: "学习如何将 Node.js 应用程序部署到 Azure App Service 中的 Web 应用。"
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 85af23df-54af-4430-8d77-a1f97e2f5b10
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c63b2b59143b57d6666910e701d8810bd517a42d


---
# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>在 Azure App Service 中创建 Node.js Web 应用
> [!div class="op_single_selector"]
> * [.Net](web-sites-dotnet-get-started.md)
> * [Node.js](web-sites-nodejs-develop-deploy-mac.md)
> * [Java](web-sites-java-get-started.md)
> * [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
> * [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> 
> 

本教程说明如何创建一个简单的 [Node.js](http://nodejs.org) 应用程序，并使用 [Git](http://git-scm.com) 将其部署到 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [Web 应用](app-service-web-overview.md)。 本教程中的说明适用于任何能够运行 Node.js 的操作系统。

学习内容：

* 如何使用 Azure 门户在 Azure App Service 中创建 Web 应用。
* 如何通过推送到 Web 应用的 Git 存储库，将 Node.js 应用程序部署到 Web 应用。

已完成的应用程序将简短的“hello world”字符串写入浏览器。

![显示“Hello World”消息的浏览器。][helloworld-completed]

有关教程以及适用于更复杂 Node.js 应用程序的示例代码，或有关如何在 Azure 中使用 Node.js 的其他主题，请参阅 [Node.js 开发人员中心](/develop/nodejs/)。

> [!NOTE]
> 若要完成本教程，您需要一个 Microsoft Azure 帐户。 如果没有帐户，可以[激活 Visual Studio 订户权益](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)，或者[注册免费试用帐户](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)。
> 
> 如果要在注册 Azure 帐户之前就开始使用 Azure 应用服务，请转到 [Try App Service](http://go.microsoft.com/fwlink/?LinkId=523751)（试用应用服务）。 在那里，可以立即在应用服务中创建短期的入门级 Web 应用 - 无需信用卡，也无需做出承诺。
> 
> 

## <a name="create-a-web-app-and-enable-git-publishing"></a>创建 Web 应用并启用 Git 发布
请按照以下步骤在 Azure App Service 中创建 Web 应用，然后启用 Git 发布。 

[Git](http://git-scm.com/) 是一个分布式版本控制系统，可用来部署 Azure 网站。 你将在本地 Git 存储库中存储你为 Web 应用编写的代码，并通过推送到远程存储库将代码部署到 Azure。 这种部署方法是应用服务 Web 应用的一项功能。  

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击 Azure 门户左上角的“+ 新建”  图标。
3. 单击“Web + 移动”，然后单击“Web 应用”。
   
    ![][portal-quick-create]
4. 在“Web 应用”框中输入 Web 应用的名称。
   
    该名称在 azurewebsites.net 域中必须是唯一的，因为 Web 应用的 URL 将是 {name}.azurewebsites.net。 如果你输入的名称不是唯一的，则会在文本框中显示一个红色的感叹号。
5. 选择一个“订阅” 。
6. 选择“资源组”或新建一个。
   
    有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。
7. 选择“应用服务计划/位置”或新建一个。
   
    有关应用服务计划的详细信息，请参阅 [Azure 应用服务计划概述](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)
8. 单击“创建” 。
   
    ![][portal-quick-create2]
   
    不久之后（通常不到一分钟），Azure 将创建出新的 Web 应用。
9. 单击“Web 应用”>“{你的新 Web 应用}”。
   
    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)
10. 在“Web 应用”边栏选项卡中，单击“部署”部件。
    
    ![][deployment-part]
11. 在“连续部署”边栏选项卡中，单击“选择源”
12. 单击“本地 Git 存储库”，然后单击“确定”。
    
    ![][setup-git-publishing]
13. 如果尚未执行此操作，请设置部署凭据。
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Web 应用”边栏选项卡中，单击“设置”>“部署凭据”。
    
    ![][deployment-credentials]
    
    b.保留“数据库类型”设置，即设置为“共享”。 创建用户名和密码。 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)
14. 在“Web 应用”边栏选项卡中，单击“设置”，然后单击“属性”。
    
    推送到远程 Git 存储库即可发布。 存储库的 URL 将在“GIT URL” 下列出。 本教程的后面部分需使用此 URL。
    
    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>本地构建和测试应用程序
在本部分中，需要创建一个 **server.js** 文件，其中包含已稍做修改的“Hello World”示例版本（来自 [nodejs.org]）。 代码将添加 process.env.PORT，作为在 Azure Web 应用中运行时要侦听的端口。

1. 创建名为 *helloworld*的目录。
2. 使用文本编辑器在 **helloworld** 目录中创建名为 *server.js* 的新文件。
3. 将以下代码复制到 **server.js** 文件，然后保存该文件：
   
        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);
4. 打开命令行，并使用以下命令在本地启动 Web 应用。
   
        node server.js
5. 打开 web 浏览器并导航到 http://localhost:1337。 
   
    显示“Hello World”的网页随即出现，如以下屏幕截图所示。
   
    ![显示“Hello World”消息的浏览器。][helloworld-localhost]

## <a name="publish-your-application"></a>发布应用程序
1. 如果你尚未安装 Git，现在请安装。
   
    有关针对你的平台的安装说明，请参阅 [Git 下载页](http://git-scm.com/download)。
2. 从命令行中，将目录更改为 **helloworld** 目录，然后输入以下命令来初始化本地 Git 存储库。
   
        git init
3. 使用以下命令将文件添加到存储库中：
   
        git add .
        git commit -m "initial commit"
4. 使用以下命令添加 Git remote，以便将更新推送到你之前创建的 Web 应用：
   
        git remote add azure [URL for remote repository]
5. 使用以下命令将更改推送到 Azure：
   
        git push azure master
   
    系统将提示你输入之前创建的密码。 输出类似于以下示例。
   
        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master
6. 若要查看你的应用，请在 Azure 门户的“Web 应用”部分中单击“浏览”按钮。
   
    ![“浏览”按钮](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)
   
    ![Azure 中的 hello world](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>发布对应用程序所做的更改
1. 在文本编辑器中打开 **server.js** 文件，将“Hello World\n”更改为“Hello Azure\n”。 
2. 保存文件。
3. 从命令行中，将目录更改为 **helloworld** 目录，然后运行以下命令：
   
        git add .
        git commit -m "changing to hello azure"
        git push azure master
   
    系统将再次提示你输入密码。
4. 刷新你从中导航到 Web 应用 URL 的浏览器窗口。
   
    ![显示“Hello Azure”的网页][helloworld-completed]

## <a name="roll-back-a-deployment"></a>回滚部署
在“Web 应用”边栏选项卡中，可以单击“设置”>“连续部署”，以查看“部署”边栏选项卡中的部署历史记录。 如果需要回滚到之前的部署，可以选择该部署，然后单击“部署详情”边栏选项卡中的“重新部署”。

## <a name="next-steps"></a>后续步骤
现已将 Node.js 应用程序部署到 Azure App Service 中的 Web 应用。 若要了解应用服务 Web 应用如何运行 Node.js 应用程序的详细信息，请参阅 [Azure App Service Web 应用：Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) 和[在 Azure 应用程序中指定 Node.js 版本](../nodejs-specify-node-version-azure-apps.md)。

Node.js 提供可由您的应用程序使用的丰富的模块生态系统。 若要了解 Web 应用如何使用模块，请参阅 [将 Node.js 模块与 Azure 应用程序一起使用](../nodejs-use-node-modules-azure-apps.md)。

如果将应用程序部署到 Azure 后遇到问题，请参阅 [如何在 Azure App Service 中调试 Node.js 应用程序](web-sites-nodejs-debug.md) ，以了解有关诊断问题的信息。

本文将使用 Azure 门户来创建 Web 应用。 你也可以使用 [Azure 命令行接口](../xplat-cli-install.md)或 [Azure PowerShell](../powershell-install-configure.md) 执行相同的操作。

有关如何在 Azure 上开发 Node.js 应用程序的详细信息，请参阅 [Node.js 开发人员中心](/develop/nodejs/)。

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png



<!--HONumber=Nov16_HO2-->



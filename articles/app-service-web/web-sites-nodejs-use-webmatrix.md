---
title: "WebMatrix を使用した Node.js Web アプリの構築と Azure へのデプロイ"
description: "WebMatrix を使用して Node.js アプリケーションを作成し、Azure App Service Web Apps にデプロイする方法を示すチュートリアル。"
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 42986058-57b8-42ea-af76-d6c6ba508608
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 54ae907409bd860f26216e85e52932bc4bd825c1


---
# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>WebMatrix を使用した Node.js Web アプリの構築と Azure へのデプロイ
このチュートリアルでは、WebMatrix を使用して Node.js アプリケーションを作成し、 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps にデプロイする方法を示します。 WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web サイトまたは Web アプリの開発に必要なものがすべて用意されています。 WebMatrix には、コードの入力補完機能、作成済みのテンプレート、Jade、LESS、CoffeeScript のエディターのサポートなど、Node.js を簡単に使用できるようにする機能が用意されています。 [WebMatrix](https://www.microsoft.com/web/webmatrix/)の詳細を確認してください。

このチュートリアルを完了すると、Azure App Service で動作する Node.js Web アプリが完成します。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure node Web サイト][webmatrix-node-completed]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="sign-into-azure"></a>Azure へのサインイン
Azure App Service で Web アプリを作成するには、次のステップに従います。

1. WebMatrix の起動
2. 初めて WebMatrix を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。  そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。  Microsoft アカウントを使用して **サインイン** することを選択します。
   
    ![[アカウントの追加]][addaccount]
3. Azure アカウントにサインアップしている場合は、Microsoft アカウントを使用してログインできます。
   
    ![Azure へのサインイン][signin]    

## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Azure の組み込みテンプレートを使用したサイトの作成
1. スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。
   
    ![テンプレート ギャラリーからの新しいサイト][sitefromtemplate]
2. **[テンプレートからサイトを作成する]** ダイアログで、**[ノード]** を選択し、**[Express サイト]** を選択します。 最後に、 **[次へ]**をクリックします。 **Express サイト** テンプレートの前提条件に不足がある場合は、インストールするよう求めるメッセージが表示されます。
   
    ![Express テンプレートの選択][webmatrix-templates]
3. Azure にサインインする場合は、ここでローカル サイト用の App Service Web アプリを作成することができます。  一意の名前を選択し、App Service Web アプリの作成先のデータ センターを選択します。 
   
    ![Azure でのサイトの作成][nodesitefromtemplateazure]
4. WebMatrix による ローカル サイトおよび App Service Web アプリの構築が終了すると、WebMatrix IDE が表示されます。
   
    ![Web Matrix IDE][webmatrix-ide]

## <a name="publish-your-application-to-azure"></a>Azure へのアプリケーションの発行
1. WebMatrix で、**[ホーム]** リボンの **[発行]** をクリックします。サイトの **[発行のプレビュー]** ダイアログ ボックスが表示されます。
   
    ![[続行]][webmatrix-node-publishpreview]
2. **[続行]**をクリックします。 発行が完了すると、App Service Web アプリの URL が WebMatrix IDE の下部に表示されます。
   
    ![発行の完了][webmatrix-publish-complete]
3. リンクをクリックして、ブラウザーで App Service Web アプリを開きます。
   
    ![Express Web アプリ][webmatrix-node-express-site]

## <a name="modify-and-republish-your-application"></a>アプリケーションの変更と再発行
アプリケーションは簡単に変更して再発行することができます。 ここでは、 **index.jade** ファイル内の見出しに単純な変更を加えて、アプリケーションを再発行します。

1. WebMatrix で、**[ファイル]** を選択し、**views** フォルダーを展開します。 **index.jade** ファイルをダブルクリックして開きます。
   
    ![WebMatrix での index.jade の表示][webmatrix-modify-index]
2. 段落の行を次のように変更します。
   
        p Welcome to #{title} with WebMatrix on Azure!
3. 変更内容を保存し、発行アイコンをクリックします。 最後に、 **[続行]** in the **[続行]** をクリックし、更新が発行されるまで待ちます。
   
    ![[続行]][webmatrix-republish]
4. 発行が完了したら、発行プロセスの完了時に返されたリンクを使用して、更新されたApp Service Web アプリを表示します。
   
    ![Azure node Web アプリ][webmatrix-node-completed]

## <a name="next-steps"></a>次のステップ
Azure に付属している Node.js のバージョンの詳細と、アプリケーションで使用するバージョンの指定方法については、「 [Azure アプリケーションでの Node.js のバージョンの指定](../nodejs-specify-node-version-azure-apps.md)」を参照してください。

Azure への展開後にアプリケーションで問題が発生した場合、問題の診断については、「 [Azure App Service での Node.js Web アプリのデバッグ方法](web-sites-nodejs-debug.md) 」を参照してください。

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png



<!--HONumber=Jan17_HO3-->



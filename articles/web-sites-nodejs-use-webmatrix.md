<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ

このチュートリアルでは、WebMatrix を使用して Node.js アプリケーションを作成し、Azure Website にデプロイする方法を示します。WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web サイトの開発に必要なものがすべて用意されています。WebMatrix には、コードの入力補完機能、作成済みのテンプレート、Jade、LESS、CoffeeScript のエディターのサポートなど、Node.js を簡単に使用できるようにする機能が用意されています。詳細については、「[Azure 用 WebMatrix の概要][Azure 用 WebMatrix の概要]」を参照してください。

このチュートリアルを完了すると、Azure で動作する Node.js Web サイトが完成します。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure node Web サイト][Azure node Web サイト]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Azure へのサインイン

Azure Website を作成するには、次のステップに従います。

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Azure Websites の機能を有効にした Azure アカウントが必要です。</p>
<p>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p>
</div>

1.  WebMatrix の起動
2.  初めて WebMatrix を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。Microsoft アカウントを使用して**サインイン**することを選択します。

    ![アカウントの追加][アカウントの追加]

3.  Azure アカウントにサインアップしている場合は、Microsoft アカウントを使用してログインできます。

    ![Azure へのサインイン][Azure へのサインイン]

## Azure の組み込みテンプレートを使用したサイトの作成

1.  スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。

    ![テンプレート ギャラリーからの新しいサイト][テンプレート ギャラリーからの新しいサイト]

2.  **[Site from Template]** ダイアログで、**[Node]** を選択し、**[Express サイト]** を選択します。最後に、**[次へ]** をクリックします。**Express サイト** テンプレートの前提条件に不足がある場合は、インストールするよう求めるメッセージが表示されます。

    ![Express テンプレートの選択][Express テンプレートの選択]

3.  Azure にサインインしている場合は、ここでローカル サイト用の Azure Websites を作成することができます。一意の名前を選択し、サイトの作成先のデータ センターを選択します。

    ![Azure でのサイトの作成][Azure でのサイトの作成]

4.  WebMatrix による Web サイトの構築が終了すると、WebMatrix IDE が表示されます。

    ![Web Matrix IDE][Web Matrix IDE]

## Azure へのアプリケーションの発行

1.  WebMatrix で、**[ホーム]** リボンの **[発行]** をクリックします。Web サイトの **[発行のプレビュー]** ダイアログ ボックスが表示されます。

    ![発行のプレビュー][発行のプレビュー]

2.  **[続行]** をクリックします。発行が完了すると、Azure の Web サイトの URL が WebMatrix IDE の下部に表示されます。

    ![発行の完了][発行の完了]

3.  リンクをクリックして、ブラウザーで Web サイトを開きます。

    ![Express Web サイト][Express Web サイト]

## アプリケーションの変更と再発行

アプリケーションは簡単に変更して再発行することができます。ここでは、**index.jade** ファイル内の見出しに単純な変更を加えて、アプリケーションを再発行します。

1.  WebMatrix で、**[ファイル]** を選択し、**views** フォルダーを展開します。**index.jade** ファイルをダブルクリックして開きます。

    ![WebMatrix での index.jade の表示][WebMatrix での index.jade の表示]

2.  2 番目の行を次のように変更します。

        p Welcome to #{title} with WebMatrix on Azure!

3.  変更内容を保存し、発行アイコンをクリックします。最後に、**[発行のプレビュー]** ダイアログの **[続行]** をクリックし、更新が発行されるまで待ちます。

    ![発行のプレビュー][1]

4.  発行が完了したら、発行プロセスの完了時に返されたリンクを使用して、更新されたサイトを表示します。

    ![Azure node Web サイト][Azure node Web サイト]

## 次のステップ

Azure に付属している Node.js のバージョンの詳細と、アプリケーションで使用するバージョンの指定方法については、「[Azure アプリケーションでの Node.js のバージョンの指定][Azure アプリケーションでの Node.js のバージョンの指定]」を参照してください。

Azure への展開後にアプリケーションで問題が発生した場合、問題の診断については、「[Azure の Web サイトでの Node.js アプリケーションのデバッグ方法][Azure の Web サイトでの Node.js アプリケーションのデバッグ方法]」を参照してください。

  [Azure 用 WebMatrix の概要]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Azure node Web サイト]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [アカウントの追加]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Azure へのサインイン]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [テンプレート ギャラリーからの新しいサイト]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [Express テンプレートの選択]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Azure でのサイトの作成]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [Web Matrix IDE]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [発行のプレビュー]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [発行の完了]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Express Web サイト]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [WebMatrix での index.jade の表示]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Azure アプリケーションでの Node.js のバージョンの指定]: /ja-jp/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Azure の Web サイトでの Node.js アプリケーションのデバッグ方法]: http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/Debug-Website/

<properties linkid="web-site-with-webmatrix" urlDisplayName="WebMatrix を使用した Web サイト" pageTitle="WebMatrix を使用した Node.js Web サイト - Azure チュートリアル" metaKeywords="" description="WebMatrix を使用して Node.js アプリケーションを作成し、Azure の Web サイトに展開する方法を示すチュートリアル。" metaCanonical="" services="web-sites" documentationCenter="Node.js" title="WebMatrix を使用した Node.js Web サイトの構築と Azure への展開" authors="" solutions="" manager="" editor="" />





# WebMatrix を使用した Node.js Web サイトの構築と Azure への展開

このチュートリアルでは、WebMatrix を使用して Node.js アプリケーションを作成し、Azure の Web サイトに展開する方法を示します。WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web サイトの開発に必要なものがすべて用意されています。WebMatrix には、コードの入力補完機能、作成済みのテンプレート、Jade、LESS、CoffeeScript のエディターのサポートなど、Node.js を簡単に使用できるようにする機能が用意されています。詳細については、「[Azure 用 WebMatrix の概要](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)」を参照してください。

このチュートリアルを完了すると、Azure で動作する Node.js Web サイトが完成します。
 
完成したアプリケーションのスクリーンショットは次のようになります。

![Azure node Web サイト][webmatrix-node-completed]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Azure へのサインイン

次の手順に従って、Azure の Web サイトを作成します。

<div class="dev-callout"><strong>注</strong>
<p>このチュートリアルを完了するには、Azure の Web サイトの機能を有効にした Azure アカウントが必要です。</p>
<p>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure の無料評価版のサイト</a>を参照してください。</p>
</div>
<br />

1. WebMatrix の起動
2. 初めて WebMatrix を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。Microsoft アカウントを使用して**サインイン**することを選択します。

	![アカウントの追加][addaccount]

3. Azure アカウントにサインアップしてある場合は、Microsoft アカウントを使用してログインできます。

	![Azure へのサインイン][signin]	


## Azure の組み込みテンプレートを使用したサイトの作成

1. スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。

	![テンプレート ギャラリーからの新しいサイト][sitefromtemplate]

2. **[Site from Template]** ダイアログで、**[Node]** を選択し、**[Express サイト]** を選択します。最後に、**[次へ]** をクリックします。**Express サイト** テンプレートの前提条件に不足がある場合は、インストールするよう求めるメッセージが表示されます。

	![Express テンプレートの選択][webmatrix-templates]

3. Azure にサインインする場合は、ここでローカル サイト用の Azure の Web サイトを作成することができます。一意の名前を選択し、サイトの作成先のデータ センターを選択します。

	![Azure でのサイトの作成][nodesitefromtemplateazure]
	
4. WebMatrix による Web サイトの構築が終了すると、WebMatrix IDE が表示されます。

	![Web Matrix IDE][webmatrix-ide]

##へのアプリケーションの発行

1. WebMatrix で、**[ホーム]** リボンの **[発行]** をクリックします。Web サイトの **[発行のプレビュー]** ダイアログ ボックスが表示されます。

	![発行のプレビュー][webmatrix-node-publishpreview]

2. **[続行]** をクリックして続行します。発行が完了すると、更新された Azure の Web サイトの URL が WebMatrix IDE の下部に表示されます。

	![発行の完了][webmatrix-publish-complete]

3. リンクをクリックして、ブラウザーで Web サイトを開きます。

	![Express Web サイト][webmatrix-node-express-site]

##アプリケーションを変更して再発行する

アプリケーションは簡単に変更して再発行することができます。ここでは、**index.jade** ファイル内の見出しに単純な変更を加えて、アプリケーションを再発行します。

1. WebMatrix で、**[ファイル]** を選択し、**views** フォルダーを展開します。**index.jade** ファイルをダブルクリックして開きます。

	![WebMatrix での index.jade の表示][webmatrix-modify-index]

2. 2 行目を次のように変更します。

		p Welcome to #{title} with WebMatrix on Azure!

3. 変更内容を保存し、発行アイコンをクリックします。最後に、**[発行のプレビュー]** ダイアログの **[続行]** をクリックし、更新が発行されるまで待ちます。

	![発行のプレビュー][webmatrix-republish]

4. 発行が完了したら、発行プロセスの完了時に返されたリンクを使用して、更新されたサイトを表示します。

	![Azure node Web サイト][webmatrix-node-completed]

##次のステップ

Azure に付属している Node.js のバージョンの詳細と、アプリケーションで使用するバージョンの指定方法については、「[Azure アプリケーションでの Node.js のバージョンの指定](http://www.windowsazure.com/ja-jp/develop/nodejs/common-tasks/specifying-a-node-version/)」を参照してください。

Azure への展開後にアプリケーションで問題が発生した場合、問題の診断については、「[Azure の Web サイトでの Node.js アプリケーションのデバッグ方法](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/Debug-Website/)」を参照してください。


[Azure Management Portal]: http://manage.windowsazure.com
[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Publishing an Azure Web site using Git]: /ja-jp/develop/nodejs/common-tasks/publishing-with-git/
[for free]: /ja-jp/pricing/free-trial
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



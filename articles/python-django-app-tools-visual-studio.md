<properties linkid="develop-python-django-with-visual-studio" UrlDisplayName="Visual Studio を使用した Django" pageTitle="Visual Studio を使用した Django (Python) - Windows Azure チュートリアル" MetaKeywords="Azure Django Web アプリケーション, Azure Django 仮想マシン" description="Windows Azure の仮想マシンでホストされる Django Web アプリケーションを作成する方法について説明するチュートリアルです。" metaCanonical="" services="cloud-services" documentationCenter="Python" title="Python Tools for Visual Studio 1.5 を使用して Django アプリケーションを作成する" authors=""  solutions="" writer="" manager="" editor=""  />





#Python Tools for Visual Studio 1.5 を使用して Django アプリケーションを作成する

**注:** このチュートリアルは [Youtube の動画](http://www.youtube.com/watch?v=UsLti4KlgAY)でご覧になることもできます。

**注:** PTVS 2.0 ベータ版に関する、[より詳しい最新のチュートリアル][]もあります。

Windows Azure の開発を容易にする各種ツールが用意されています。
このチュートリアルは、Windows Azure を使用した経験がない読者を対象に作成されています。
このガイドでは、クラウドで動作する Django アプリケーションを作成します。

学習内容:

-   基本的な Django アプリケーションの作成方法
-   Django テスト サーバーを使用して Django アプリケーションをローカルで実行し、デバッグする方法
-   コンピューティング エミュレーターで Django アプリケーションをローカル実行する方法
-   Windows Azure にアプリケーションを発行および再発行する方法

このチュートリアルを実行して、単純な Hello World Web アプリケーション
を作成します。このアプリケーションは Web ロールのインスタンスにホストされます。
Windows Azure で実行する場合は、このロール自体が専用の
仮想マシン (VM) にホストされます。

完成したアプリケーションのスクリーンショットは次のようになります。

![](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a id="setup"> </a>開発環境の設定

Windows Azure アプリケーションを開発する前に、ツールを入手して、開発環境を設定する必要があります。Windows Azure SDK for Python の入手とインストールの詳細については、「[How to Install Python (Python のインストール方法)][]」を参照してください。

**注:** このチュートリアルでは、Python 2.7 と Django 1.4 が必要です。これらのバージョンは、Windows Azure SDK for Python の現在のバージョンに含まれています。

**注:** コンピューティング エミュレーターまたは Windows Azure へのデプロイでは、Visual Studio の完全バージョン (統合シェルはサポートされません) が必要です。

## 新しい Django アプリケーションの作成

新しい Django アプリケーションを作成するには、最初に Visual Studio を起動し、次に **[ファイル] メニューの [新しいプロジェクト]** を使用して新しいプロジェクトを作成します。[Python] タブ (最上位レベルまたは [他の言語] のレベルにあります) を見つけて、Django アプリケーション テンプレートを選択します。



![新しい Python プロジェクト テンプレート](./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png)



**[OK] をクリック**すると、最初の Django アプリケーションが作成されます。


![最初の Django プロジェクトが表示されている Visual Studio](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png)

次に、最初の Django アプリケーションの開発を開始します。モジュール ノードを右クリックし、**[新しい Django アプリケーションの追加]** をクリックして、プロジェクト内で新しいアプリケーションを設定することができます。

![新しいアプリケーションを追加するメニュー項目](./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png)

その後で、アプリケーションに新しい名前を入力できます。

![新しいアプリケーションの名前を追加するプロンプト](./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png)

アプリケーションの名前を入力し、**[OK]** をクリックすると、新しいアプリケーションがプロジェクトに追加されます。

![新しいアプリケーションが追加されたソリューション エクスプローラー](./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png)

ここで、**settings.py** を更新して、アプリケーションを登録します。これを行うと、Django によって、アプリケーションの [テンプレート] ディレクトリに追加されるテンプレート ファイルが自動的に検出されます。INSTALLED_APPS セクションにアプリケーション名を追加します。

	'DjangoApplication.MyFirstApp',

![INSTALLED_APPS で settings.py にアプリケーションを追加](./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png)

次に、アプリケーションの **views.py** にコードをいくつか追加します。これにより、単純なテンプレート ファイルが返されます。

	from django.http import HttpResponse
	from django.template.loader import render_to_string
	def home(request):
		return HttpResponse(render_to_string(
											'index.html',
											{'content': 'Hello World'}
											))


![INSTALLED_APPS で settings.py にアプリケーションを追加](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png)

次に、このビューにアクセスしたときにレンダリングされる単純なテンプレート ファイルを追加します。そのためには、[テンプレート] フォルダーを右クリックし、**[新しい項目の追加]** をクリックします。

![[テンプレート] フォルダーへの新しい項目の追加](./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png)

ここでテンプレートの一覧から [Django HTML テンプレート] を選択し、ファイル名に「**index.html**」と入力します。

![[テンプレート] フォルダーへの新しい項目の追加](./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png)

その後で、テンプレートがプロジェクトに追加されて開きます。このとき、テンプレートのタグで構文の先頭部分が強調表示されているのを確認できます。

![ソリューション エクスプローラーに追加されたテンプレート](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png)

この時点で、テンプレートの更新を開始して、レンダリングされる HTML に変更を加えることができます。この作業を行うときに、便利な Intellisense 機能を利用できます。

![Django フィルターに対応したテンプレートの Intellisense](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png)

大文字の使用は有効にしても無効にしてもかまいません。どちらの場合も、このチュートリアルの結果が変わることはありません。最後に、**urls.py** に URL のパターンでビューを登録する必要があります。これは、**urlpatterns** に追加します。

	url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![URL の登録](./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png)

## テスト サーバーでのアプリケーションのローカル実行

この時点で、最初の Django アプリケーションの作成は完了しています。これで、**F5 キーを押す**だけで、アプリケーションをローカルで実行することができます。

![ブラウザーとテスト サーバーでの Django Hello World](./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png)

これにより、Django の **manage.py** を実行する Python インタープリターが開始され、テスト サーバーが動作します。テスト サーバーが適切に起動されると、Web サイトを表示するために Web ブラウザーも起動されます。F5 キーで起動したため、このアプリケーションはデバッガー内で実行されます。これにより、ビュー コードなどの任意の Python コード内やテンプレート ファイル内にブレークポイントを設定できます。

![テンプレートのブレークポイントで停止するデバッガー](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png)

これで、**停止ボタンをクリック**して、Windows Azure コンピューティング エミュレーターでの実行に移行できます。

## エミュレーターでのアプリケーションのローカル実行	

コンピューティング エミュレーター内で実行するには、Windows Azure デプロイ プロジェクトを Django プロジェクト用のソリューションに追加するだけです。

**注:** コンピューティング エミュレーターまたは Windows Azure へのデプロイでは、Visual Studio の完全バージョン (統合シェルはサポートされません) が必要です。

これを実行するには、ソリューション エクスプローラーで Django プロジェクト ノードを右クリックし、**[Windows Azure クラウド サービス プロジェクトの追加]** をクリックします。

![デプロイ プロジェクトの追加](./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png)

このコマンドを実行すると、ソリューション エクスプローラーで新しく追加されたプロジェクトを確認できます。

![デプロイ プロジェクトを追加した結果](./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png)

この新しいプロジェクトは、ソリューションのスタートアップ プロジェクトとしてマークされます。この時点で、コンピューティング エミュレーターで実行できるように **Visual Studio を管理者として再起動する**必要があります。再起動後は、**F5 キーを押す**だけで、アプリケーションが実行され、コンピューティング エミュレーターにデプロイされます。

![デプロイ プロジェクトを追加した結果](./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png)

ここで、このチュートリアルと同じ Web ページを現在表示していますが、URL が若干異なっていることに気付くでしょう。また、Django テスト サーバーを実行する python.exe が存在していません。代わりに、FastCGI ゲートウェイを使用し、IIS を介して Django を実行しています。FastCGI ゲートウェイは、Visual Studio 内から作業するときに自動的に取り込まれ設定されます。

コンピューティング エミュレーターで実行するとき、アプリケーションをすばやく反復処理することができます。そのためには、Visual Studio に切り替え、ファイルを更新し、Web ブラウザーを最新の情報に更新します。直ちに結果を確認できます。

## Windows Azure へのアプリケーションのデプロイ

これで、プロジェクトを Windows Azure にデプロイする準備ができました。デプロイするには、ソリューション エクスプローラーで Windows Azure デプロイ プロジェクトを右クリックし、**[発行] をクリック**するだけです。

![パッケージ アプリケーションのメニュー](./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png)

[発行] をクリックすると、Windows Azure へのサインインを要求されます。この画面で既存の資格情報をインポートすることもできますし、新しい資格情報を設定することもできます。

![パッケージのサブスクリプション](./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png)

資格情報を選択すると、[Windows Azure 発行設定] 画面が表示されます。デプロイの処理方法についてさまざまなオプションを選択できます。また、オプションを選択せずに **[発行] をクリック**することもできます。

![パッケージの設定](./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png)

ここで、アプリケーションが設定されデプロイされるのを待機する必要があります。

![パッケージのデプロイ](./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png)

設定がすべて完了したら、DNS 名の下にあるリンクをクリックして、クラウドで実行されている Web サイトを表示できます。


![クラウド内の Django アプリケーション](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)


[How to Install Python (Python のインストール方法)]: ../python-how-to-install/
[より詳しい最新のチュートリアル]: ../web-sites-python-create-deploy-django-app/



<properties
	pageTitle="Linux VM で Ruby on Rails Web サイトをホストする | Microsoft Azure"
	description="Linux 仮想マシンを使用して、Azure で Ruby on Rails ベースの Web サイトをセットアップしてホストします。"
	services="virtual-machines-linux"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="robmcm"/>

# Azure VM での Ruby on Rails Web アプリケーション

このチュートリアルでは、Linux 仮想マシンを使用して、Azure で Ruby on Rails Web サイトをホストする方法について説明します。

このチュートリアルは、Ubuntu Server 14.04 LTS を使用して検証されました。他の Linux ディストリビューションを使用する場合は、Rails のインストール手順が異なる場合があります。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

## Azure VM の作成

最初に、Linux イメージを使用して Azure VM を作成します。

VM を作成するには、Azure クラシック ポータルまたは Azure コマンド ライン インターフェイス (CLI) を使用できます。

### Microsoft Azure 管理ポータル

1. [Azure クラシック ポータル](http://manage.windowsazure.com)にサインインします。
2. **[新規]**、**[Compute]**、**[仮想マシン]**、**[簡易作成]** の順にクリックします。Linux イメージを選択します。
3. パスワードを入力します。

VM がプロビジョニングされたら、VM 名をクリックし、**[ダッシュボード]** をクリックします。**[SSH の詳細]** で、SSH エンドポイントを見つけます。

### Azure CLI

「[Linux を実行する仮想マシンの作成][vm-instructions]」の手順に従います。

VM がプロビジョニングされたら、次のコマンドを実行して SSH エンドポイントを取得できます。

	azure vm endpoint list <vm-name>  

## Ruby on Rails のインストール

1. SSH を使用して VM に接続します。

2. SSH セッションから、次のコマンドを使用して VM に Ruby をインストールします。

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	インストールには数分かかる場合があります。インストールが完了したら、次のコマンドを使用して、Ruby がインストールされたことを確認します。

		ruby -v

	インストールされている Ruby のバージョンが返されます。

3. 次のコマンドを使用して Rails をインストールします。

		sudo gem install rails --no-rdoc --no-ri -V

	ここでは、--no-rdoc フラグと --no-ri フラグを使用してドキュメントのインストールをスキップしています。これにより、インストールに要する時間が短縮されます。このコマンドは実行に時間がかかる可能性があります。-V を追加すると、インストールの進行状況に関する情報が表示されます。

## アプリの作成と実行

SSH を使用してログインしている状態で、次のコマンドを実行します。

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

[new](http://guides.rubyonrails.org/command_line.html#rails-new) コマンドは、新しい Rails アプリケーションを作成します。[server](http://guides.rubyonrails.org/command_line.html#rails-server) コマンドは、Rails に付属している WEBrick Web サーバーを起動します (実際の運用では、Unicorn、Passenger などの他のサーバーを使用できます)。

次のような出力が表示されます。

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## エンドポイントの追加

1. [Azure クラシック ポータル][management-portal]に移動し、目的の VM を選択します。

	![仮想マシンの一覧][vmlist]

2. ページの上部で **[エンドポイント]** を選択し、ページ下部で **[+ エンドポイントの追加]** をクリックします。

	![エンドポイント ページ][endpoints]

3. **[エンドポイントの追加]** ダイアログで、[スタンドアロン エンドポイントの追加] を選択し、**[次へ]** 矢印をクリックします。

	![新しいエンドポイントのダイアログ][new-endpoint1]

3. 次のダイアログ ページで、次の情報を入力します。

	* **名前**: HTTP

	* **プロトコル**: TCP

	* **パブリック ポート**: 80

	* **プライベート ポート**: 3000

	これにより、プライベート ポート 3000 にトラフィックをルーティングするパブリック ポート 80 が作成されます。ルーティング先のプライベート ポートは、Rails がリッスンしています。

	![新しいエンドポイントのダイアログ][new-endpoint]

4. チェック マークをクリックして、エンドポイントを保存します。

5. **"更新が進行中です"** というメッセージが表示されます。このメッセージが消えると、エンドポイントがアクティブになります。この時点で仮想マシンの DNS 名に移動すると、アプリケーションをテストできます。Web サイトは次のようになります。

	![既定の rails ページ][default-rails-cloud]

## 次のステップ

このチュートリアルでは、ほとんどの手順を手作業で行いました。通常、運用環境では、開発用コンピューターで作成したアプリを Azure VM にデプロイします。また、運用環境では、Apache や NginX などの別のサーバー プロセスと組み合わせて Rails アプリケーションをホストすることがほとんどです。これらのサーバーは、複数の Rails アプリケーション インスタンスへの要求のルーティングを処理すると共に、静的リソースを提供します。詳細については、http://rubyonrails.org/deploy/ を参照してください。

Ruby on Rails の詳細については、[Ruby on Rails のガイド][rails-guides]を参照してください。

Ruby アプリケーションから Azure のサービスを使用する方法については、次のトピックを参照してください。

* [BLOB を使用して非構造化データを保存する][blobs]

* [テーブルを使用してキー/値のペアを保存する][tables]

* [Content Delivery Network を使用して高帯域幅コンテンツを配信する][cdn-howto]

<!-- WA.com links -->
[blobs]: ../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png

<!---HONumber=AcomDC_0511_2016-->
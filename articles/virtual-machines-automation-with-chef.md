<properties 
	pageTitle="Chef で Azure 仮想マシンのデプロイを自動化する"
	description="Chef で Azure 仮想マシンのデプロイを自動化する方法" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="diegoviso" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="diviso"/>

# Chef で Azure 仮想マシンのデプロイを自動化する

Chef は自動化と必要な状態の構成を提供する優れたツールです。 

Chef は最新の cloud-api リリースで Azure とのシームレスな統合を提供しており、1 つのコマンドで構成状態をプロビジョニングして展開できます。

この記事では、Chef 環境を設定し、Azure 仮想マシンをプロビジョニングする方法と、ポリシーまたは「CookBook」を作成し、それを Azure 仮想マシンに導入する方法について説明します。

早速始めましょう。

## Chef の基礎

開始する前に Chef の基本的な概念を把握しておくことをお勧めします。優れた記事が <a href="http://www.chef.io/chef" target="_blank">こちら</a> にあるので、先に読んでおくことをお勧めします。基礎については開始する前に要約します。

次の図は、大まかな Chef アーキテクチャを示しています。

![][2]

Chef には 3 つの主要なアーキテクチャ コンポーネント**Chef サーバー、Chef クライアント (ノード)**、**Chef ワークステーション**があります。

**Chef サーバー**は管理ポイントとなります。Chef サーバーにはホスト型ソリューションと内部設置型ソリューションの 2 つのオプションがあります。ここではホスト型ソリューションを使用します。

**Chef クライアント (ノード)** は管理対象のサーバーに置かれるエージェントです。

**Chef ワークステーション**は管理者用のワークステーションで、ポリシーを作成したり、管理コマンドを実行します。Chef ワークステーションから **knife** コマンドを実行してインフラストラクチャを管理します。

また「Cookbook」と「Recipe」の概念もあります。これらの概念が、実際に定義して、サーバーに適用するポリシーになります。

## ワークステーションを準備する

まずはワークステーションを準備します。ここでは、標準の Windows ワークステーションを使用します。構成ファイルと Cookbook を保存するディレクトリを作成する必要があります。

最初に **C:\chef** というディレクトリを作成します。 

次に 2 個目のディレクトリ **c:\chef\cookbooks** を作成します。

Azure 設定ファイルをダウンロードして Chef が Azure のサブスクリプションと通信できるようにする必要があります。

パブリッシュ設定はこちらからダウンロードしてください。 <a href="https://manage.windowsazure.com/publishsettings/" target="_blank">https://manage.windowsazure.com/publishsettings/</a>

パブリッシュ設定ファイルを **C:\chef** に保存します。

## 管理された Chef アカウントの作成

ホストされる Chef アカウントにサインアップします。 <a href="https://manage.chef.io/signup" target="_blank">https://manage.chef.io/signup</a>

サインアップ プロセス中、新しい組織を作成するように尋ねられます。

![][3]

組織を作成したら、スターター キットをダウンロードします。

![][4]

**注:** キーがリセットされる旨の警告が表示されても、インフラストラクチャがまだ構成されていないので、そのまま進んで構いません。

このスターター キットの zip ファイルには、組織の構成ファイルとキーが含まれています。

## Chef ワークステーションの構成

chef-starter.zip を **C:\chef** に解凍します。

**chef-starter\chef-repo.chef** にあるすべてのファイルを **c:\chef** にコピーします。

これでディレクトリは次のようになるはずです。

![][5]

c:\chef のルートには Azure パブリッシュ ファイルを含む 4 つのファイルがあるはずです。

PEM ファイルには組織と管理者の通信用秘密キーが含まれ、**knife.rb** ファイルには knife 構成が含まれています。**knife.rb** ファイルを編集する必要があります。

任意のエディターでファイルを開き、パスから「/../」を削除して「cookbook_path」を変更し、次のようになるようにします。

	cookbook_path  ["#{current_dir}/cookbooks"]

また、Azure パブリッシュ設定ファイルの名前を反映する次の行を追加します。   

	knife[:azure_publish_settings_file] = "yourfilename.publishsettings" 

これで knife.rb ファイルは次のようになるはずです。

![][6]

これらの行によって、Knife が c:\chef\cookbooks の cookbook ディレクトリを参照し、Azure 操作中に Azure パブリッシュ設定を使用するようになります。

## Chef 開発キットのインストール

次に ChefDK (Chef 開発キット) をダウンロードしてインストールし、Chef Workstation を設定します。

<a href="http://downloads.getchef.com/chef-dk/windows" target="_blank">http://downloads.getchef.com/chef-dk/windows</a>

![][7]

手順は簡単です。c:\opscode の既定の場所にインストールします。このインストールは約 10 分かかります。

パスの変数に C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;c:\users\yourusername.chefdk\gem\ruby\2.0.0\bin のエントリーが含まれていることを確認します。

ない場合は必ずこのパスを追加してください。

**パスの順序は重要なので注意してください。**opscode パスが正しい順序でない場合、問題が生じます。

続行する前にワークステーションを再起動します。

次に Knife Azure 拡張機能をインストールします。これで Knife に「Azure プラグイン」が提供されます。

次のコマンドを実行します。

	chef gem install knife-azure --pre

**注:** -pre 引数は、最新の API セットへのアクセスを提供する Knife Azure プラグインの最新 RC バージョンを確実に受け取るようにします。

さまざまな依存関係も同時にインストールされる場合があります。

![][8]


すべてが正しく構成されたことを確認するには、次を実行します。

	knife azure image list

すべてが正しく構成されていると、利用できる Azure イメージの一覧がスクロール表示されます。

おめでとうございます。ワークステーションが設定されました。

## Cookbook の作成

Cookbookは管理するクライアント上で実行する一連のコマンドを定義するために Chef で使用します。Cookbook は簡単に作成でき、chef generate cookbook コマンドを使用して Cookbook のテンプレートを生成します。ポリシーで IIS を自動的に展開させるため、Cookbook webserver を呼び出します。

C:\Chef ディレクトリで次のコマンドを実行します。

	chef generate cookbook webserver

これで、ディレクトリ **C:\Chef\cookbooks\webserver** に一連のファイルが生成されます。次に、管理する VM で Chef クライアントに実行させる一連のコマンドを定義する必要があります。

コマンドはファイル **default.rb** に保存されています。このファイルでは、IIS をインストールして、起動し、wwwroot フォルダーにテンプレートをコピーする一連のコマンドを定義します。

**C:\chef\cookbooks\webserver\recipes\default.rb** を変更して次の行を追加します。

	powershell_script 'Install IIS' do
 		action :run
 		code 'add-windowsfeature Web-Server'
	end

	service 'w3svc' do
 		action [ :enable, :start ]
	end

	template 'c:\inetpub\wwwroot\Default.htm' do
 		source 'Default.htm.erb'
 		rights :read, 'Everyone'
	end

完了したらファイルを保存します。

## テンプレートの作成

既に説明したように、default.html ページとして使用するテンプレート ファイルを生成する必要があります。

次のコマンドを実行してテンプレートを生成します。

	chef generate template webserver Default.htm

ファイル **C:\chef\cookbooks\webserver\templates\default\Default.htm.erb** に移動して、ファイルを編集します。

シンプルな「Hello World」 html コードを追加してファイルを保存します。

## Chef サーバーに Cookbook をアップロードする

このステップでは、ローカル コンピューターに作成した Cookbook のコピーを、Chef がホストするサーバーにアップロードします。アップロードが完了すると、Cookbook は [Policy (ポリシー)] タブに表示されます。

	knife cookbook upload webserver

![][9]

## Knife Azure で仮想マシンを展開する

Azure 仮想マシンを展開して、IIS Web サービスと既定のWeb ページをインストールする「Webserver」Cookbook を適用します。

これを実行するには **knife azure server create** コマンドを使用します。

次にコマンドの例を示します。

	knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

パラメーターを見ればすぐにわかります。特定の変数に置き換えて実行してください。

**注:** コマンド ラインに -tcp-endpoints パラメーターを使用し、エンドポイント ネットワーク フィルター ルールも自動化しています。これでポート 80 と 3389 を開き、Web ページと RDP セッションへのアクセスを提供します。

コマンドを実行したら、Azure ポータルに移動し、コンピューターでプロビジョニングが開始することを確認します。

![][13]

コマンド プロンプトに戻ります。

![][10]

展開が完了したら、ポート 80 で Web サービスに接続できるようになるはずです。ポートは knife azure コマンドで VM をプロビジョニングした際に開いています。この VM はクラウド サービスで唯一の VM なので、クラウド サービスの URL で接続します。

![][11]

ご覧のように、クリエイティブな html コードにしました。

また、ポート 3389 を経由して Azure ポータルから RDP セッションを通じて接続できます。

お役に立てれば幸いです。今すぐ Azure でコードを工夫したインフラストラクチャを始めてください。

Diego Viso [MSFT]

<!--Image references-->
[2]: ./media/virtual-machines-automation-with-chef/2.png
[3]: ./media/virtual-machines-automation-with-chef/3.png
[4]: ./media/virtual-machines-automation-with-chef/4.png
[5]: ./media/virtual-machines-automation-with-chef/5.png
[6]: ./media/virtual-machines-automation-with-chef/6.png
[7]: ./media/virtual-machines-automation-with-chef/7.png
[8]: ./media/virtual-machines-automation-with-chef/8.png
[9]: ./media/virtual-machines-automation-with-chef/9.png
[10]: ./media/virtual-machines-automation-with-chef/10.png
[11]: ./media/virtual-machines-automation-with-chef/11.png
[13]: ./media/virtual-machines-automation-with-chef/13.png


<!--Link references-->

<!--HONumber=47-->

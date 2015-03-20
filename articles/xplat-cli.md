<properties 
	pageTitle="Azure クロス プラットフォーム コマンド ライン インターフェイス" 
	description="Azure クロスプラットフォーム コマンドライン インターフェイスをインストールして構成し、Azure サービスを監視します。" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/16/2015" 
	ms.author="rasquill"/>

# Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Cross-Platform CLI" class="current">クロス プラットフォーム CLI</a></div>

Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli)、Azure Platform を使用するためのクロス プラットフォーム コマンドのオープン ソースのセットを提供します。xplat-cli で使用できる機能の多くは、Web サイトの管理、仮想マシン、モバイル サービス、SQL データベース、Azure Platform の他のサービスなど、Azure の管理ポータルで使用できるものと同じです。

xplat-cli は JavaScript で記述されており、Node.js が必要です。Azure SDK for Node.js を使用して実装されており、Apache 2.0 ライセンスに基づいてリリースされています。プロジェクトのリポジトリは[https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat)です。

このドキュメントでは、Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールして構成する方法、およびこれを使用して Azure Platform で基本的なタスクを実行する方法について説明します。

##このドキュメントの内容

* [Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする方法](#install)
* [Azure サブスクリプションに接続する方法](#configure)
* [Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する方法(#use)
* [Azure クロス プラットフォーム コマンド ライン インターフェイスのスクリプトを作成する方法(#script)
* [その他のリソース](#additional-resources)

## <a id="install">Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする方法</a>

xplat-cli のインストール方法は 2 つあります。Windows および OS X のインストーラー パッケージを使用する方法と、**npm** コマンドを使用する方法 (Node.js をインストール済みの場合) です。xplat-cli をインストールしたら、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から **azure** コマンドを使用して、xplat-cli コマンドにアクセスできます。

###インストーラーの使用

次のインストーラー パッケージを使用できます。

* [Windows インストーラー][windows インストーラー]

* [OS X インストーラー][mac インストーラー]

###npm の使用

Node.js はまだシステムにインストールされている場合、xplat-cli をインストールするには、次のコマンドを使用します。

	npm install azure-cli -g

>[AZURE.NOTE] 使用する必要があります `sudo` __npm__ コマンドを正常に実行します。

Node.js がインストールされていない場合に最初にインストールする必要があると思います。Xamarin ツールには、クロス プラットフォーム アプリケーションを開発するための Visual Studio 統合と Xamarin Studio、スタンドアロン IDE がサポートされています。Linux システムの場合、Node.js をインストールして、以下の手順に従って **npm** を使用して xplat-cli をインストールするか、ソースから構築します。ソースは[http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409)です。ソースからの構築の詳細については、アーカイブにある INSTALL ファイルを参照してください。

####ディストリビューションを使用する node.js と npm のインストール[よう](http://en.wikipedia.org/wiki/Dpkg)パッケージの管理 
いずれかのこれらのパッケージの最も一般的な使用、 [(傾向) のパッケージ化ツールを高度な](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool)またはその他のツールに基づいて、 `.deb`パッケージ形式です。例としては、Ubuntu Debian も、その他の多くがあります。 

これらのパッケージのより最近のほとんどは、インストールが必要な**nodejs レガシ**取得するために、正しく指定**npm** 、azure cli をインストールするツールです。次のコードは、インストールするコマンド**npm** Ubuntu 14.04 で適切にします。
	
	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Ubuntu 12.04 などの古い、ディストリビューションのいくつかは、現在のバイナリ配布の node.js をインストールする必要があります。次のコードはその方法をインストールして使用**curl**です。 

>[AZURE.NOTE] Joyent インストールの手順から取得されます。 ここにコマンド[ここ](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)です。ただしを使用する場合**sudo**を常に注意してくださいをインストールするように、正確に期待される機能実行する前に検証して、スクリプトを確認するパイプのターゲットとして**sudo**です。
	
	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

####ディストリビューションを使用する node.js と npm のインストール[rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)パッケージの管理

RPM ベースのディストリビューションでの node.js をインストールするには、EPEL リポジトリを有効にする必要があります。CentOS 7 でのインストールに関するベスト プラクティスを次のコードに示します。(最初の線の下に、'-' (ハイフン) が重要です)。

	su -     
	yum update [enter]
	yum upgrade -y [enter] 
	yum install epel-release [enter]
	yum install nodejs [enter] 
	yum install npm [enter] 
	npm install -g azure-cli  [enter]


xplat-cli をインストールしたら、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から **azure** コマンドを使用して、xplat-cli コマンドにアクセスできます。インストールの最後に、次のような情報が表示されます。

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [AZURE.NOTE] Node.js と npm できますから Windows にインストールできるも<a href="http://nodejs.org/">http://nodejs.org/</a>です。

## <a id="configure">Azure サブスクリプションに接続する方法</a>

xplat-cli の一部のコマンドは Azure サブスクリプションがなくても機能しますが、多くのコマンドにはサブスクリプションが必要です。サブスクリプションで機能するように xplat-cli を構成するには、次のいずれかの方法があります。

* 発行設定ファイルをダウンロードして使用する。

または

* 組織のアカウントを使用して Azure にログインする。ログインすると、Azure Active Directory を使用して資格情報が認証されます。

以下の情報を考慮して、適切な認証方法を選択してください。

*  ログインによる方法では、サブスクリプションへのアクセスの管理が簡単になりますが、自動化は利用できません。資格情報がタイムアウトになり、ログインが再び必要になるためです。

	> [AZURE.NOTE] ログイン方法は、組織のアカウントでのみ機能します。組織のアカウントは、組織で管理されるユーザーであり、組織の Azure Active Directory テナントで定義されています。組織のアカウントが現在なく、Azure サブスクリプションへのログインに Microsoft アカウントを使用している場合は、次に示している手順を使用して組織のアカウントを簡単に作成できます。
	> 
	> 1. ログイン、 [Azure 管理ポータル][ポータル]の] をクリック**Active Directory**です。
	> 
	> 2. ディレクトリが存在しない場合は選択**ディレクトリの作成**し、必要な情報を指定します。
	> 
	> 3. ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーが組織のアカウントです。
	> 
	>     ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は別の手順で使用するため保管しておいてください。
	> 
	> 4. 管理ポータルで、次のように選択します。**設定**クリックして**管理者**です。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、組織のアカウントで Azure サブスクリプションを管理できるようになります。
	> 
	> 5. 最後に、Azure ポータルからログアウトし、新しい組織のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。
	>
	>Microsoft Azure で組織のアカウントの詳細については、次を参照してください。[組織としての Microsoft Azure へのサインアップ][signuporg]です。

*  発行設定ファイルによる方法では、管理タスクを実行するための証明書がインストールされますが、サブスクリプションと証明書が有効であることが必要です。この方式は、長時間実行するタスクの自動化に適しています。サブスクリプション情報を一度ダウンロードしてインポートしたら、再度提供する必要はありません。ただし、この方法では、サブスクリプションへのアクセスの管理が難しくなります。証明書にアクセスできるだれもがサブスクリプションを管理できるためです。

認証とサブスクリプションの管理の詳細については、次を参照してください。 ["アカウント ベースの認証と証明書ベースの認証の間の違いは"][authandsub]です。

アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、次を参照してください。 [Azure 無料評価版][無料評価版]です。

###ログインによる方法の使用

組織のアカウントを使用してログインするには、次のコマンドを使用します。

	azure login -u username -p password

を対話的に (たとえば、画面上にマスクされたパスワードをする場合) を入力してログインする場合は、次のコマンドを使用します。

	azure login

資格情報を求めるメッセージが表示され、パスワードの文字はマスクされます。

> [AZURE.NOTE] これは、最初にこれらの資格情報を使用してログオンしたとき場合、は、認証トークンをキャッシュすることを確認することを確認が表示されます。このプロンプトを使用していた場合にも発生、 `azure logout`コマンド以下で説明します。自動化のシナリオには、このプロンプトをバイパスするには、'-q' を持つパラメーター、 `azure login`コマンドです。
>
> Azure サブスクリプションにアクセスするための情報が格納されている組織アカウントを認証するときに、 `.azure`ディレクトリ、 `user`ディレクトリ。 `user`ディレクトリは、オペレーティング システムによって保護されますただし、暗号化する追加の手順を実行することをお勧め、 `user`ディレクトリ。そのためには、次の操作を行います。

ログアウトするには、次のコマンドを使用します。

	azure logout -u <username>

> [AZURE.NOTE] 場合は、アカウントに関連付けられているサブスクリプションは Active Directory で認証されたのみ、ログアウト、ローカル プロファイルからサブスクリプション情報が削除されます。ただし、サブスクリプション用に発行設定ファイルもインポートされている場合は、ログアウトするとローカル プロファイルから Active Directory 関連の情報のみが削除されます。

> [AZURE.NOTE] アカウントを使用した認証では、次のコマンドは正しく機能しません。
> 
> * `azure vm`
> * `azure network`
> * `azure mobile`
> 
> これらのコマンドを使用する必要がある場合は、次のセクションで説明しているように、発行設定ファイルを使用して Azure に対して認証する必要があります。

### 発行設定ファイルによる方法の使用

アカウントの発行設定をダウンロードするには、次のコマンドを使用します。

	azure account download

既定のブラウザーを開いてを Azure 管理ポータルにサインインするように求めこのことができます。サインインした後、 `.publishsettings`ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

> [AZURE.NOTE] アカウントが複数の Azure Active Directory テナントに関連付けられている場合は、発行の設定ファイルのダウンロード対象となる Active Directory を選択するよう促されることがあります。
> 
> ダウンロード ページを使用するか、Azure の管理ポータルにアクセスして選択を行った後、選択した Active Directory は、ポータルおよびダウンロード ページによって使用される既定になります。既定を確定した後、クリックして選択ページに戻るように促すメッセージがダウンロード ページの上部に表示されます。選択されたページに戻るには、用意されているリンクを使用します。

次に、インポート、 `.publishsettings`ファイルは、次のコマンドを実行して置き換える '[.publishsettings ファイルへのパス]' へのパス、 `.publishsettings`ファイル。

	azure account import [path to .publishsettings file]

> [AZURE.NOTE] 発行の設定を Azure サブスクリプションにアクセスするための情報はインポートすると、 `.azure`ディレクトリ、 `user`ディレクトリ。 `user`ディレクトリは、オペレーティング システムによって保護されますただし、暗号化する追加の手順を実行することをお勧め、 `user`ディレクトリ。そのためには、次の操作を行います。
>
> * Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。
> * Mac の場合、ディレクトリに対して FileVault を有効にします。
> * Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同等の機能が用意されています。

インポートした後、発行の設定を削除する必要があります、 `.publishsettings`ファイルをコマンド ライン ツールでは不要になったあり、セキュリティ上のリスクをサブスクリプションにアクセスするために使用されることができます。

###複数のサブスクリプション

複数の Azure サブスクリプションがある場合は、ログインすると、資格情報に関連付けられているすべてのサブスクリプションへのアクセスが許可されます。発行設定ファイルを使用する場合、 `.publishsettings`ファイルはすべてのサブスクリプションの情報が格納されます。いずれの方法でも、操作の実行時に xplat-cli によって使用される既定のサブスクリプションとして 1 つのサブスクリプションが選択されます。サブスクリプションを表示するには、既定値のどちらとしても使用と、 `azure account list`コマンド。このコマンドでは、次のような情報が返されます。

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

上のリストでは、**Current** 列に現在の既定のサブスクリプションとして Azure-sub-1 が表示されています。既定のサブスクリプションを変更する次のように使用します。、 `azure account set`コマンド、および、既定値にするサブスクリプションを指定します。次に例を示します。

	azure account set Azure-sub-2

この結果、既定のサブスクリプショは Azure-sub-2 に変更されます。 

> [AZURE.NOTE] 既定のサブスクリプションの変更はすぐに有効になり、グローバル設定です。新しい xplat-cli コマンドの実行には、同じコマンドライン インスタンスからでも別のインスタンスからでも、この新しい既定のサブスクリプションが使用されます。

xplat-cli で、既定のサブスクリプションを変更せずに既定ではないサブスクリプションを使用する場合は、"--subscription" オプションを使用して、その操作に使用するサブスクリプションの名前を指定します。

<h2><a id="use"></a>Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する方法</h2>

Xplat-cli を使用してアクセス、 `azure`コマンド。使用できるコマンドの一覧については、次のように使用します。、 `azure`コマンドをパラメーターなし。次のようなヘルプ情報が表示されます。

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Windows Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.0
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

ここに表示されているトップ レベルのコマンドには、Azure の特定の領域で機能するコマンドが含まれています。たとえば、 `azure account`コマンドなど、Azure サブスクリプションに関連するコマンドに含まれる、 `download`と `import`設定が以前に使用します。

ほとんどのコマンドとして書式設定された ' azure < コマンド >< 操作 >[パラメーター]'、サービスやアカウント構成などのオブジェクトに対する操作を行います。その他のコマンドのサブコマンドの提供し、形式に従う 'azure < コマンド >< サブコマンド >< 操作 >[パラメーター]' です。次のコマンド例は、アカウント構成に対する操作です。

* インポート済みのサブスクリプションを表示する。

		azure account list

* 複数のサブスクリプションをインポート済みで、1 つのサブスクリプションを既定に設定する。

		azure account set <subscription>

'-ヘルプ' または '-h' パラメーターは、特定のコマンドのヘルプの表示を使用することができます。または、"azure help [command] [options]" の形式で指定しても、同じ情報が返されます。たとえば、次のコマンドでは、すべて同じ情報が返されます。

	azure account set --help

	azure account set -h

	azure help account set

コマンドに必要なパラメーターがわからない場合は "--help"、"-h"、または "azure help [command]" を使用してヘルプを参照してください。

###構成モードの設定

xplat-cli では、データベース サーバー、データベース、Web サイトなど、ユーザー管理のエンティティである個々の_リソース_に対して管理操作を実行できます。これは xplat-cli の既定の動作モードであり、**Azure サービス管理**と呼ばれています。ただし、複数のリソースで構成された複雑なソリューションがあるときは、ソリューション全体を 1 つの論理単位として管理できるようにすると便利です。

プレビューが導入されています (_r) group_、1 つの論理単位としてのリソースのグループの管理をサポートする、**リソース マネージャー**としての Azure リソースを管理する新しい方法です。 

>[AZURE.NOTE] リソース マネージャーは、現在プレビュー版であり、Azure サービス管理と同じレベルの管理機能は提供されていません。

新しいリソース マネージャーをサポートする xplat-cli を使用するとこれらの管理の間で切り替える 'modes'を使用して、 `azure config mode`コマンド。

xplat-cli では、Azure サービス管理モードに既定で設定されています。リソース マネージャー モードに切り替えるには、次のコマンドを使用します。

	azure config mode arm

Azure サービス管理モードに戻すには、次のコマンドを使用します。

	azure config mode asm 

>[AZURE.NOTE] リソース マネージャー モードと Azure サービス管理モードでは、相互に排他的です。つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。

Xplat-cli を使用してリソース マネージャーでの使用方法の詳細については、次を参照してください。 [Azure クロスプラット フォーム コマンドライン インターフェイス リソース マネージャーを使用して][xplatarm]です。

###Azure サービス管理モードでのサービスの操作

xplat-cli を使用すると、Azure サービスを簡単に管理できます。この例では、xplat-cli を使用して Azure Web サイトを管理する方法について説明します。

1. 新しい Azure Website を作成するには、次のコマンドを使用します。**mywebsite** は、一意の名前に置き換えてください。

		azure site create mywebsite

	Web サイトが作成される場所を指定するように求められます。地理的に近いリージョンを選択します。このコマンドが完了したら、web サイトが http://mywebsite.azurewebsites.net で使用できる (置換**mywebsite**指定した名前を持つ)。

	> [AZURE.NOTE] かどうか Git プロジェクトのソース管理を使用するを指定できます、'-git' パラメーターをこの web サイトの Azure での Git リポジトリを作成します。Git リポジトリがないディレクトリからこのコマンドを実行した場合、Git リポジトリの再初期化も行われます。使用して、Azure の web サイトへの展開をプッシュするために使用する、__azure__ という名前の Git リモートも作成されます、 `git push azure master`コマンド。

	> [AZURE.NOTE] エラーが発生する場合を 'site'、azure ではない xplat-cli がリソース グループのモードで最も可能性の高いコマンドします。変更するモードに戻すを使用、 `azure config mode asm`コマンド。

2. サブスクリプションのすべての Web サイトを表示するには、次のコマンドを使用します。

		azure site list

	このリストには、前の手順で作成したサイトが含まれます。

2. Web サイトを停止するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

		azure site stop mywebsite

	コマンドが完了したら、ブラウザーを更新するとサイトが停止したことを確認できます。

3. Web サイトを開始するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

		azure site start mywebsite

	コマンドが完了したら、ブラウザーを更新するとサイトが開始したことを確認できます。

4. Web サイトを削除するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

		azure site delete mywebsite

	コマンドが完了すると、使用して、 `azure site list`コマンドを web サイトはもう存在しないことを確認します。

<h2><a id="script"></a>Azure クロス プラットフォーム コマンド ライン インターフェイスのスクリプトを作成する方法</h2>

xplat-cli を使用して手動でコマンドを発行できますが、お使いのシステムで使用できるコマンド ライン インタープリターやその他のコマンド ライン ユーティリティの機能を利用して、複雑な自動化ワークフローを作成することもできます。たとえば、次のコマンドはすべての Azure Web サイトを停止します。

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

この例の web サイト一覧は、パイプを使用して、 `grep`コマンドは、文字列の各行を調べて 'Running'です。一致した行にパイプし、 `awk`コマンドを呼び出す `azure site stop`を停止するサイト名 (実行中のサイト名) に渡される 2 番目の列を使用しています。

これは、コマンドの連結方法を示した例ですが、コマンド ライン インタープリターのスクリプト機能を使用して、さらに複雑なスクリプトを作成することもできます。コマンドライン インタープリターの種類ごとに、スクリプト機能や構文が異なります。Linux および OS X を含め、UNIX ベースのシステム用には Bash が最も広く使用されているコマンド ライン インタープリターでしょう。

Bash でのスクリプト作成方法の詳細については、次を参照してください。、[高度な Bash-scripting Guide][高度な bash]です。

OS X または Linux ベースのシステムのスクリプトに関するより一般的な情報は、次を参照してください。 [Shell script][スクリプト]です。

バッチ ファイルを使用して Windows ベースのシステムのスクリプトの作成方法については、次を参照してください。[コマンド ライン リファレンス A ～ Z][バッチ]です。

### 結果について

スクリプトの作成時、コマンド出力をキャプチャしたり、出力を別のコマンドに渡したり、出力に特別な操作 (特定の値の確認など) を実行したりすることがあります。xplat-cli は出力を STDOUT および STDERR に生成します。すべての行の文字列のプレフィックスが付いた `info:` for informational status messages, or `data:` for data returned about a service; however, you can instruct the xplat-cli to return more verbose information by using the `--verbose`または '-v' パラメーターです。これは、文字列で追加の情報を返します。 `verbose:`.

For example, the following output is returned from the `azure site list`コマンド。

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

"--verbose" または "-v" パラメーターを指定すると、次のような情報が返されます。

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

注意して、 `verbose:` information appears to be JSON formatted data. You can use the `--json`ネイティブなど、JSON を認識するユーティリティを使用している場合は、JSON 形式で情報を返すパラメーター [jsawk](https://github.com/micha/jsawk)または[jq](http://stedolan.github.io/jq/)です。次に例を示します。

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

このコマンドは Web サイトのリストを JSON として取得してから jsawk を使用してサイト名を取得し、最後に xargs を使用して、各サイトの削除コマンドを実行します。このサイト名はパラメーターとして渡されています。

>[AZURE.NOTE] '-Json' パラメーターの状態やデータの情報の生成をブロックする (文字列が付きます `info:` and `data:`). For example, if the `--json`でパラメーターが使用される、 `azure site create`、ようにこのコマンドはデータを返さない以外の場合、出力は返されません' 情報:' です。

###エラーの操作

xplat-cli では、エラー情報を STDERR にログ出力しますが、エラーに関する追加情報が、スクリプトを実行したディレクトリにある **azure.err** ファイルに記録されます。このファイルに情報が記録された場合、次のような文字列が STDOUT に書き込まれます。

	info:    Error information has been recorded to azure.err

###終了ステータス

必須パラメーターが不足している場合、一部の xplat-cli コマンドは 0 以外の終了ステータスを返しません。代わりに、ユーザーの入力が要求されます。使用する場合など、 `azure site create`ないサイト名の場合、新しい web サイトを作成するコマンドをまたは '-場所' パラメーターは指定されているこれらの値を指定するように求められます。

終了ステータスに依存するスクリプトを作成する場合は、使用する xplat-cli コマンドがユーザー入力を求めないことを確認してください。

<h2><a id="additional-resources"></a>その他のリソース</h2>

* 詳細については、xplat-cli で、ソース コードの問題の報告をダウンロードしたり、プロジェクトへの協力を参照してください、 [、Azure クロス プラットフォーム コマンド ライン インターフェイスの GitHub リポジトリ](https://github.com/WindowsAzure/azure-sdk-tools-xplat)です。

* Xplat-cli または Azure を使用して問題が発生した場合にアクセスして、 [Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home)です。

* Azure での詳細については、次を参照してください。 [http://azure.microsoft.com/](http://azure.microsoft.com)です。




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure の Web サイト]: ../media/freetrial.png
[プレビュー機能を選択します。]: ../media/antares-iaas-preview-02.png
[サブスクリプションを選択します。]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[xplatarm]: /documentation/articles/xplat-cli-azure-resource-manager/
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/

<!--HONumber=47-->

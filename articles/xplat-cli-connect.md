<properties
	pageTitle="CLI から Azure へのログイン | Microsoft Azure"
	description="Mac、Linux、および Windows 用の Azure コマンドライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続

Azure CLI は、Azure プラットフォームで使用できるオープン ソース、クロスプラットフォームのコマンド群です。この記事では、すべての CLI コマンドを使用できるように、Azure CLI から Azure サブスクリプションに接続する方法について説明します。CLI をまだインストールしていない場合は、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。



Azure CLI からサブスクリプションに接続する方法には、次の 2 つがあります。

* **職場または学校のアカウント ID か、Microsoft アカウント ID を使用して Azure にログインする** - この方法では、どちらかの種類のアカウント ID を使用して認証を行います。最新の CLI では、多要素認証が有効になっているアカウントの対話型認証もサポートしています。対話形式でログインした後は、リソース マネージャーを使用することも、従来の (サービス管理) コマンドを使用することもできます。

* **発行設定ファイルをダウンロードして使用する** - この方法では、管理タスクを実行できるようにするための証明書がローカル コンピューターにインストールされます。ただし、サブスクリプションと証明書が有効であることが必要です。この方法では、従来の (サービス管理) コマンドしか使用できません。

認証とサブスクリプション管理の詳細については、「[What's the difference between account-based authentication and certificate-based authentication (アカウント ベースの認証と証明書ベースの認証の違い)][authandsub]」を参照してください。

Azure アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][free-trial]を参照してください。

>[AZURE.NOTE] バージョン 0.9.10 より前のバージョンの Azure CLI を使用している場合、`azure login` コマンドは職場または学校のアカウント ID でのみ使用できます。Microsoft アカウント ID は機能しません。ただし、Azure CLI バージョン 0.9.10 以降では、対話型の `azure login` コマンドを使用して、任意の ID でアカウントにログインできます。
>
CLI バージョン 0.9.9 以降は、多要素認証をサポートしています。



## 対話型のログイン方法の使用

引数なしで `azure login` コマンドを使用して、次のいずれかによる対話型認証を行います。

- 多要素認証を必要とする職場または学校のアカウント ID
- Microsoft アカウント ID (リソース マネージャー デプロイ モード機能にアクセスする場合)

> [AZURE.NOTE]  どちらの場合も、認証と承認は Azure Active Directory を使用して行われます。Microsoft アカウントの ID を使用する場合、ログ プロセスは Azure Active Directory の既定のドメインにアクセスします(無料試用版にサインアップした場合は、Azure Active Directory によってアカウントの既定のドメインが作成されていることに気づかないことがあります)。

対話型ログインは簡単です。「`azure login`」と入力し、次に示すように画面の指示に従います。

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

上記で提供されたコードをコピーし、ブラウザーを開いて http://aka.ms/devicelogin に移動します。コードを入力すると、使用する ID のユーザー名とパスワードを入力するように求められます。このプロセスが完了すると、コマンド シェルによってログイン プロセスが完了します。次のような内容が表示されます。

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## 職場または学校のアカウントでの非対話型ログインの使用


非対話型のログイン方法は、職場または学校のアカウント (*組織アカウント*とも呼ばれます) でのみ機能します。このアカウントは組織によって管理され、組織の Azure Active Directory で定義されます。[組織アカウントを作成](#create-an-organizational-account)することも (組織アカウントがない場合)、[Microsoft アカウント ID から職場または学校の ID を作成](./virtual-machines/virtual-machines-windows-create-aad-work-id.md)することもできます。そのためには、次のように `azure login` コマンドにユーザー名、またはユーザー名とパスワードを指定する必要があります。

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

パスワードを求められたら、入力します。

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* **ログアウト**するには、次のコマンドを使用します。

		azure logout -u <username>

	アカウントに関連付けられたサブスクリプションが Active Directory のみを使用して認証された場合は、ログアウトするとローカル プロファイルからサブスクリプション情報が削除されます。ただし、サブスクリプション用に発行設定ファイルもインポートされている場合は、ログアウトするとローカル プロファイルから Active Directory 関連の情報のみが削除されます。

## 発行設定ファイルによる方法の使用

従来の (サービス管理) CLI コマンドを使用するだけで十分である場合は、発行設定ファイルを使用して接続できます。

* アカウントの**発行設定ファイルをダウンロード**するには、次のコマンドを使用します。

		azure account download

これにより、既定のブラウザーが開き、[Azure クラシック ポータル][portal]にサインインするよう求められます。サインインした後、`.publishsettings` ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

> [AZURE.NOTE] アカウントが複数の Azure Active Directory テナントに関連付けられている場合は、発行の設定ファイルのダウンロード対象となる Active Directory を選択するよう促されることがあります。
>
> ダウンロード ページを使用して選択するか、Azure クラシック ポータルにアクセスして選択すると、選択した Active Directory が、クラシック ポータルおよびダウンロード ページで既定として使用されようになります。既定を確定した後、'__click here to return to the selection page (選択ページに戻るにはここをクリックしてください)__' というテキストが、ダウンロード ページの上部に表示されます。選択されたページに戻るには、用意されているリンクを使用します。

* **発行設定ファイルをインポートするには**、次のコマンドを実行します。

		azure account import <path to your .publishsettings file>

	発行設定をインポートしたら、`.publishsettings` ファイルを削除します。このファイルは Azure CLI では不要であり、第三者によってサブスクリプションヘのアクセスに使用されるセキュリティ上のリスクがあるためです。


## 複数のサブスクリプション

複数の Azure サブスクリプションがある場合は、Azure に接続すると、資格情報に関連付けられているすべてのサブスクリプションへのアクセスが許可されます。1 つのサブスクリプションが既定として選択され、操作の実行時に Azure CLI によって使用されます。`azure account list` コマンドを使用すると、すべてのサブスクリプションを表示でき、既定のサブスクリプションも確認できます。このコマンドでは、次のような情報が返されます。

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

上のリストでは、**Current** 列に現在の既定のサブスクリプションとして Azure-sub-1 が表示されています。既定のサブスクリプションを変更するには、`azure account set` コマンドを使用して、既定にするサブスクリプションを指定します。次に例を示します。

	azure account set Azure-sub-2

この結果、既定のサブスクリプションは Azure-sub-2 に変更されます。

> [AZURE.NOTE] 既定のサブスクリプションの変更は、直ちにグローバルで適用されます。新しい Azure CLI コマンドを実行するときには、同じコマンドライン インスタンスからでも別のインスタンスからでも、この新しい既定のサブスクリプションが使用されます。

Azure CLI で既定以外のサブスクリプションを使用して、既定のサブスクリプションを変更しないようにするには、コマンドで `--subscription` オプションを使用して、その操作に使用するサブスクリプションの名前を指定します。

Azure サブスクリプションに接続すると、その Azure CLI コマンドの使用を開始することができます。

## CLI 設定の格納

職場または学校のアカウントを使用してログインするか、発行設定をインポートすると、CLI プロファイルとログが、`user` ディレクトリの `.azure` ディレクトリに格納されます。`user` ディレクトリはオペレーティング システムによって保護されますが、追加の作業を行って `user` ディレクトリを暗号化することをお勧めします。そのためには、次の操作を行います。

* Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。
* Mac の場合、ディレクトリに対して FileVault を有効にします。
* Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同様の機能が用意されています。

## その他のリソース

* [Azure CLI でのサービス管理 (クラシック) コマンドの使用][cliasm]

* [Azure CLI でのリソース マネージャー コマンドの使用][cliarm]

* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、[GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli) のページを参照してください。

* Azure CLI、または Azure を利用していて問題が発生した場合は、[Azure のフォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home)をご覧ください。





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0323_2016-->
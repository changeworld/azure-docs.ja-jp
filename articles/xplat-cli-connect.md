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
	ms.date="04/08/2016"
	ms.author="danlep"/>

# Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続

Azure CLI は、Azure プラットフォームで使用できるオープン ソース、クロスプラットフォームのコマンド群です。この記事では、Azure サブスクリプションに Azure CLI を接続するための Azure アカウント資格情報を入力する方法について説明します。CLI をまだインストールしていない場合は、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。Azure サブスクリプションがない場合は、[無料アカウント](http://azure.microsoft.com/free/)を数分で作成することができます。

Azure CLI からサブスクリプションに接続する方法には、次の 2 つがあります。

* **職場または学校のアカウントまたは Microsoft アカウントID を使用して Azure にログインする** - アカウント ID のいずれかのタイプを使用して、CLI バージョン 0.9.10 以降で `azure login` コマンドを実行し、Azure Active Directory を通じて認証します。CLI (バージョン 0.9.9 以降) では、Multi-Factor Authentication が有効になっているアカウントの Web ポータルを通じた対話型認証もサポートしています。また、`azure login` コマンドを実行し、Azure Active Directory アプリケーションのサービス プリンシパルを認証します。これは自動化されたサービスの実行に便利です。サポートされているアカウント ID でログインした後は、Azure Resource Manager モードまたは Azure Service Management モード コマンドのいずれかを実行できます。

* **発行設定ファイルをダウンロードして使用する** - この方法では、管理タスクを実行できるようにするための証明書がローカル コンピューターにインストールされます。ただし、サブスクリプションと証明書が有効であることが必要です。この方法では、Azure Service Management モード コマンドしか使用できません。

>[AZURE.NOTE] バージョン 0.9.10 より前のバージョンの Azure CLI を使用している場合、`azure login` コマンドは職場または学校のアカウントでのみ使用できます。Microsoft アカウント ID は機能しません。ただし、必要に応じて、[ Microsoft アカウント ID から職場または学校の ID を作成](virtual-machines/virtual-machines-windows-create-aad-work-id.md)できます。

異なるアカウント ID と Azure サブスクリプションに関する背景については、「[How Azure subscriptions are associated with Azure Active Directory (Azure サブスクリプションを Azure Active Directory に関連付ける方法)](./active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## Azure ログインを使用して、Web ポータルを通じて対話型認証を行う

引数なしで `azure login` コマンドを使用して、次のいずれかによる対話型認証を行います。

- Multi-Factor Authentication を必要とする職場または学校のアカウント ID (*組織アカウント*とも呼ばれる)
- Microsoft アカウント ID (Resource Manager モード コマンドにアクセスする場合)

> [AZURE.NOTE]  どちらの場合も、認証と承認は Azure Active Directory を使用して行われます。Microsoft アカウントの ID を使用する場合、ログ プロセスは Azure Active Directory の既定のドメインにアクセスします(無料 Azure アカウントにサインアップした場合は、Azure Active Directory によってアカウントの既定のドメインが作成されていることに気づかないことがあります)。

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

## 組織アカウントのユーザー名とパスワードによる Azure ログインの使用


Multi-Factor Authentication を必要としない職場または学校のアカウントを使用する場合は、ユーザー名パラメーターまたはユーザー名とパスワードの両方を持つ `azure login` コマンドを使用して認証します。次の例では、組織アカウントのユーザー名が渡されます。

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

パスワードを求められたら、入力します。

初めてこれらの資格情報を使用してログインする場合は、認証トークンをキャッシュするかどうかの確認を求められます。このプロンプトは、`azure logout` コマンド (この記事の後半で説明) を以前に使用したことがある場合にも表示されます。自動化のシナリオでこのプロンプトが表示されないようにするには、`azure login` に `-q` パラメーターを付けて実行します。

   

## サービス プリンシパルによる Azure ログインの使用

Active Directory アプリケーションのサービス プリンシパルを作成し、サービス プリンシパルにサブスクリプションに対するアクセス許可がある場合は、`azure login` コマンドを実行してサービス プリンシパルを認証できます。シナリオによっては、`azure login` コマンドの明示的なパラメーターとして、または CLI スクリプトまたはアプリケーション コードを通じてサービス プリンシパルの資格情報を入力できます。証明書を使用して、Automation のシナリオで非対話的にサービス プリンシパルを認証することもできます。詳細と例については、「[Authenticating a service principal with Azure Resource Manager (Azure Resource Manager でのサービス プリンシパルの認証)](resource-group-authenticate-service-principal.md)」を参照してください。

## 発行設定ファイルによる方法の使用

Azure Service Management モード CLI コマンドを使用するだけで十分である場合は、発行設定ファイルを使用して接続できます。

* アカウントの**発行設定ファイルをダウンロード**するには、次のコマンドを使用します (Service Management モードでのみ使用可能)。

		azure account download

    これにより、既定のブラウザーが開き、[Azure クラシック ポータル](https://manage.windowsazure.com)にサインインするよう求められます。サインインした後、`.publishsettings` ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

    > [AZURE.NOTE] アカウントが複数の Azure Active Directory テナントに関連付けられている場合は、発行の設定ファイルのダウンロード対象となる Active Directory を選択するよう促されることがあります。

    ダウンロード ページを使用して選択するか、Azure クラシック ポータルにアクセスして選択すると、選択した Active Directory が、クラシック ポータルおよびダウンロード ページで既定として使用されようになります。既定を確定した後、'__click here to return to the selection page (選択ページに戻るにはここをクリックしてください)__' というテキストが、ダウンロード ページの上部に表示されます。選択されたページに戻るには、用意されているリンクを使用します。

* **発行設定ファイルをインポートするには**、次のコマンドを実行します。

		azure account import <path to your .publishsettings file>

	>[AZURE.IMPORTANT]発行設定をインポートした後は、`.publishsettings` ファイルを削除する必要があります。このファイルは Azure CLI では不要であり、第三者によってサブスクリプションヘのアクセスに使用されるセキュリティ上のリスクがあるためです。

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

Azure サブスクリプションに接続すると、その Azure CLI コマンドの使用を開始し、Azure リソースを操作することができます。

## CLI コマンド モード

Azure CLI には、異なるコマンド セットを使用して、Azure リソースを操作するための次の 2 つのコマンド モードが用意されています。

* **Azure Resource Manager モード** - Resource Manager デプロイ モデルでの Azure リソースを操作するため。このモードを設定するには、`azure config mode arm` を実行します。

* **Azure Service Management モード** - クラシック デプロイ モデルでの Azure リソースを操作するため。このモードを設定するには、`azure config mode asm` を実行します。

最初にインストールされた場合、CLI は Service Management モードです。

>[AZURE.NOTE]Azure リソース マネージャー モードと Azure サービス管理モードは互いに排他的です。つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。

## CLI 設定の格納

`azure login` コマンドを使用してログインするか、発行設定をインポートすると、CLI プロファイルとログが、`user` ディレクトリの `.azure` ディレクトリに格納されます。`user` ディレクトリはオペレーティング システムによって保護されますが、追加の作業を行って `user` ディレクトリを暗号化することをお勧めします。そのためには、次の操作を行います。

* Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。
* Mac の場合、ディレクトリに対して FileVault を有効にします。
* Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同様の機能が用意されています。

## ログアウト

ログアウトするには、次のコマンドを使用します。

	azure logout -u <username>

アカウントに関連付けられたサブスクリプションが Active Directory のみを使用して認証された場合は、ログアウトするとローカル プロファイルからサブスクリプション情報が削除されます。ただし、サブスクリプション用に発行設定ファイルもインポートされている場合は、ログアウトするとローカル プロファイルから Active Directory 関連の情報のみが削除されます。
## 次のステップ

* Azure CLI コマンドを使用するには、「[Azure CLI commands in Azure Resource Manager mode (Azure Resource Manager モードでの Azure CLI コマンド)](./virtual-machines/azure-cli-arm-commands.md)」および「 [Azure CLI commands in Azure Service Management mode (Azure Service Management モードでの Azure CLI コマンド)](virtual-machines-command-line-tools.md)」を参照してください。

* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、[GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli) のページを参照してください。

* Azure CLI、または Azure を利用していて問題が発生した場合は、[Azure のフォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home)をご覧ください。

<!---HONumber=AcomDC_0427_2016-->
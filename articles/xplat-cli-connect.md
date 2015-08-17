<properties
	pageTitle="Azure コマンド ライン インターフェイス (Azure CLI) からのログイン | Microsoft Azure"
	description="Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続

Azure CLI は、Azure プラットフォームで使用できるオープン ソース、クロスプラットフォームのコマンド群です。このドキュメントでは、Azure CLI から Azure サブスクリプションに接続する方法について説明します。インストールの方法については、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。

<a id="configure"></a>
## 方法: Azure サブスクリプションに接続する

Azure CLI で提供されるほとんどのコマンドで、Azure アカウントへの接続が必要になります。サブスクリプションで使用できるように Azure CLI を構成する方法には、次の 2 つがあります。

* 職場または学校のアカウント (組織アカウントとも呼ばれます) を使用して Azure にログインする。この方法では、Azure Active Directory を使用して資格情報を認証します。

または

* 発行設定ファイルをダウンロードして使用する。この方法では、管理タスクを実行するための証明書がインストールされますが、サブスクリプションと証明書が有効であることが必要です。

認証とサブスクリプション管理の詳細については、「[What's the difference between account-based authentication and certificate-based authentication (アカウント ベースの認証と証明書ベースの認証の違い)][authandsub]」を参照してください。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][free-trial]を参照してください。

> [AZURE.NOTE]最も柔軟性の高い高度な Azure サービスでは、Azure リソース マネージャー、つまり [ARM モード](xplat-cli-azure-resource-manager.md)が使用されます。ARM モードを使用するには、以下に示すログイン方法で、職場または学校のアカウントを使用して Azure に接続する必要があります。

### ログインによる方法の使用

このログイン方法は、職場または学校のアカウントでのみ機能します。このアカウントは組織によって管理され、組織の Azure Active Directory で定義されます。

> [AZURE.NOTE]現在、職場または学校のアカウントがなく、個人用アカウントを使用して Azure サブスクリプションにログインしている場合は、次の手順を使用して簡単に職場または学校のアカウントを作成できます。
>
> 1. [Azure ポータル][portal]にログインして、**[Active Directory]** を選択します。
>
> 2. ディレクトリがない場合は、**[ディレクトリの作成]** を選択し、求められる情報を入力します。
>
> 3. ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーは、職場または学校のアカウントです。
>
>     ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は後で必要になるため保存しておきます。
>
> 4. Azure ポータルで **[設定]**、**[Administrators]** の順に選択します。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、職場または学校のアカウントで Azure サブスクリプションを管理できるようになります。
>
> 5. 最後に、Azure ポータルからログアウトし、新しい職場または学校のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。
>
> 6. 職場または学校のアカウントを使用してログインしたとき、自分のサブスクリプションが表示されることを確認してください。
>
>職場または学校のアカウントの詳細については、「[Azure への組織としてのサインアップ][signuporg]」を参照してください。

職場または学校のアカウントを使用して Azure CLI からログインするには、次のコマンドを使用します。

	azure login -u <username>

パスワードを求められたら、入力します。

> [AZURE.NOTE]初めてこれらの資格情報を使用してログインする場合は、認証トークンをキャッシュするかどうかの確認を求められます。このプロンプトは、下で説明している `azure logout` コマンドを以前に使用した場合にも表示されます。自動化のシナリオでこのプロンプトが表示されないようにするには、`-q` パラメーターを付けて `azure login` コマンドを使用してください。

ログアウトするには、次のコマンドを使用します。

	azure logout -u <username>

> [AZURE.NOTE]アカウントに関連付けられたサブスクリプションが Active Directory のみを使用して認証された場合は、ログアウトするとローカル プロファイルからサブスクリプション情報が削除されます。ただし、サブスクリプション用に発行設定ファイルもインポートされている場合は、ログアウトするとローカル プロファイルから Active Directory 関連の情報のみが削除されます。

### 発行設定ファイルによる方法の使用

> [AZURE.NOTE]この方法を使用して接続する場合は、Azure サービス管理 (ASM モード) コマンドのみを使用できます。

アカウントの発行設定をダウンロードするには、次のコマンドを使用します。

	azure account download

既定のブラウザーが開き、[Azure ポータル][portal]にサインインするよう促されます。サインインすると、`.publishsettings` ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

> [AZURE.NOTE]アカウントが複数の Azure Active Directory テナントに関連付けられている場合は、発行の設定ファイルのダウンロード対象となる Active Directory を選択するよう促されることがあります。
>
> ダウンロード ページを使用して選択するか、Azure ポータルにアクセスして選択すると、選択した Active Directory が、ポータルおよびダウンロード ページで既定として使用されようになります。既定を確定した後、'__click here to return to the selection page (選択ページに戻るにはここをクリックしてください)__' というテキストが、ダウンロード ページの上部に表示されます。選択されたページに戻るには、用意されているリンクを使用します。

次のコマンドを実行して `.publishsettings` をインポートします。

	azure account import <path to your .publishsettings file>

発行設定をインポートしたら、`.publishsettings` ファイルを削除します。このファイルは Azure CLI では不要であり、第三者によってサブスクリプションヘのアクセスに使用されるセキュリティ上のリスクがあるためです。

> [AZURE.NOTE]職場または学校のアカウントを使ってログインするか、発行設定をインポートすると、Azure サブスクリプションにアクセスするための情報が、`user` ディレクトリの `.azure` ディレクトリに格納されます。`user` ディレクトリはオペレーティング システムによって保護されますが、追加の作業を行って `user` ディレクトリを暗号化することをお勧めします。そのためには、次の操作を行います。
>
> * Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。
> * Mac の場合、ディレクトリに対して FileVault を有効にします。
> * Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同等の機能が用意されています。

### 複数のサブスクリプション

複数の Azure サブスクリプションがある場合は、Azure に接続すると、資格情報に関連付けられているすべてのサブスクリプションへのアクセスが許可されます。1 つのサブスクリプションが既定として選択され、操作の実行時に Azure CLI によって使用されます。`azure account list` コマンドを使用すると、すべてのサブスクリプションを表示でき、既定のサブスクリプションも確認できます。このコマンドでは、次のような情報が返されます。

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

上のリストでは、**Current** 列に現在の既定のサブスクリプションとして Azure-sub-1 が表示されています。既定のサブスクリプションを変更するには、`azure account set` コマンドを使用して、既定にするサブスクリプションを指定します。次に例を示します。

	azure account set Azure-sub-2

この結果、既定のサブスクリプションは Azure-sub-2 に変更されます。

> [AZURE.NOTE]既定のサブスクリプションの変更は、直ちにグローバルで適用されます。新しい Azure CLI コマンドを実行するときには、同じコマンドライン インスタンスからでも別のインスタンスからでも、この新しい既定のサブスクリプションが使用されます。

Azure CLI で既定以外のサブスクリプションを使用して、既定のサブスクリプションを変更しないようにするには、コマンドで `--subscription` オプションを使用して、その操作に使用するサブスクリプションの名前を指定します。

Azure サブスクリプションに接続すると、その Azure CLI コマンドの使用を開始することができます。詳細については、[Azure CLI の使用方法](xplat-cli.md)に関するページを参照してください。

<a id="additional-resources"></a>
## その他のリソース

* [Azure CLI でのサービス管理 (ASM モード) コマンドの使用][cliasm]に関するページ

* [Azure CLI でのリソース管理 (ARM モード) コマンドの使用][cliarm]に関するページ

* Azure CLI の詳細、ソース コードのダウンロード、問題の報告、プロジェクトへの協力については、[GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli) のページを参照してください。

* Azure CLI、または Azure を利用していて問題が発生した場合は、[Azure のフォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home)をご覧ください。

* Azure の詳細については、[http://azure.microsoft.com/](http://azure.microsoft.com) を参照してください。





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=August15_HO6-->
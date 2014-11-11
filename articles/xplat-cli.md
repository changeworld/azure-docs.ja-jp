<properties linkid="script-xplat-intro" urlDisplayName="Azure Cross-Platform Command-Line Interface" pageTitle="The Azure Cross-Platform Command-Line Interface" title="The Azure Cross-Platform Command-Line Interface" metaKeywords="Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli" description="Install and configure the Azure Cross-Platform Command-Line Interface to manage Azure Services" metaCanonical="http://www.windowsazure.com/ja-jp/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="carolz" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="carolz" />

# Azure クロス プラットフォーム コマンド ライン インターフェイスのインストールと構成

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/ja-jp/manage/install-and-configure-cli/" title="クロス プラットフォーム CLI" class="current">クロス プラットフォーム CLI</a></div>

Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) は、Azure Platform を使用するための、オープン ソースのクロス プラットフォーム コマンドのセットです。xplat-cli で使用できる機能の多くは、Web サイトの管理、仮想マシン、モバイル サービス、SQL データベース、Azure Platform の他のサービスなど、Azure の管理ポータルで使用できるものと同じです。

xplat-cli は JavaScript で記述されており、Node.js が必要です。Azure SDK for Node.js を使用して実装されており、Apache 2.0 ライセンスに基づいてリリースされています。プロジェクトのリポジトリは [][]<https://github.com/WindowsAzure/azure-sdk-tools-xplat></a> にあります。

このドキュメントでは、Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールして構成する方法、およびこれを使用して Azure Platform で基本的なタスクを実行する方法について説明します。

## このドキュメントの内容

-   [方法: Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする][方法: Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする]
-   [方法: Azure サブスクリプションに接続する][方法: Azure サブスクリプションに接続する]
-   [方法: Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する][方法: Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する]
-   [方法: Azure クロス プラットフォーム コマンド ライン インターフェイスでスクリプトを使用する][方法: Azure クロス プラットフォーム コマンド ライン インターフェイスでスクリプトを使用する]
-   [その他のリソース][その他のリソース]

## <span id="install"></span></a>方法: Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする

xplat-cli のインストール方法は 2 つあります。Windows および OS X のインストーラー パッケージを使用する方法と、**npm** コマンドを使用する方法 (Node.js をインストール済みの場合) です。

Xamarin ツールには、クロス プラットフォーム アプリケーションを開発するための Visual Studio 統合と Xamarin Studio、スタンドアロン IDE がサポートされています。Linux システムの場合、Node.js をインストールして、以下の手順に従って **npm** を使用して xplat-cli をインストールするか、ソースから構築します。ソースは、[][1]<http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409></a> から入手できます。ソースからの構築の詳細については、アーカイブにある INSTALL ファイルを参照してください。

xplat-cli をインストールしたら、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から **azure** コマンドを使用して、xplat-cli コマンドにアクセスできます。

### インストーラーの使用

次のインストーラー パッケージを使用できます。

-   [Windows インストーラー][Windows インストーラー]

-   [OS X インストーラー][OS X インストーラー]

### npm の使用

Node.js がインストールされているシステムの場合は、次のコマンドを使用して xplat-cli をインストールします。

    npm install azure-cli -g

> [WACOM.NOTE] **npm** コマンドを実行するには、`sudo` の使用が必要になる場合があります。

これにより、xplat-cli および必要な依存関係がインストールされます。インストールの最後に、次のような情報が表示されます。

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

> [WACOM.NOTE] Node.js は <http://nodejs.org/> からインストールできます。

## <span id="Configure"></span></a>方法: Azure サブスクリプションに接続する

xplat-cli の一部のコマンドは Azure サブスクリプションがなくても機能しますが、多くのコマンドにはサブスクリプションが必要です。サブスクリプションで機能するように xplat-cli を構成するには、次のいずれかの方法があります。

-   発行設定ファイルをダウンロードして使用する。

または

-   組織のアカウントを使用して Azure にログインする。ログインすると、Azure Active Directory を使用して資格情報が認証されます。

以下の情報を考慮して、適切な認証方法を選択してください。

-   ログインによる方法では、サブスクリプションへのアクセスの管理が簡単になりますが、自動化は利用できません。資格情報がタイムアウトになり、ログインが再び必要になるためです。

    > [WACOM.NOTE] ログインによる方法は組織のアカウントでのみ機能します。組織のアカウントは、組織で管理されるユーザーであり、組織の Azure Active Directory テナントで定義されています。組織のアカウントが現在なく、Azure サブスクリプションへのログインに Microsoft アカウントを使用している場合は、次に示している手順を使用して組織のアカウントを簡単に作成できます。
    >
    > 1.  [Azure の管理ポータル][Azure の管理ポータル]にログインし、**[Active Directory]** をクリックします。
    >
    > 2.  ディレクトリがない場合は、**[ディレクトリの作成]** を選択し、求められる情報を入力します。
    >
    > 3.  ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーが組織のアカウントです。
    >
    >     ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は別の手順で使用するため保管しておいてください。
    >
    > 4.  管理ポータルで **[設定]**、**[Administrators]** の順に選択します。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、組織のアカウントで Azure サブスクリプションを管理できるようになります。
    >
    > 5.  最後に、Azure ポータルからログアウトし、新しい組織のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。
    >
    > Microsoft Azure での組織のアカウントの詳細については、「[Microsoft Azure への組織としてのサインアップ][Microsoft Azure への組織としてのサインアップ]」を参照してください。

-   発行設定ファイルによる方法では、管理タスクを実行するための証明書がインストールされますが、サブスクリプションと証明書が有効であることが必要です。この方式は、長時間実行するタスクの自動化に適しています。サブスクリプション情報を一度ダウンロードしてインポートしたら、再度提供する必要はありません。ただし、この方法では、サブスクリプションへのアクセスの管理が難しくなります。証明書にアクセスできるだれもがサブスクリプションを管理できるためです。

認証とサブスクリプション管理の詳細については、「[What's the difference between account-based authentication and certificate-based authentication (アカウント ベースの認証と証明書ベースの認証の違い)][What's the difference between account-based authentication and certificate-based authentication (アカウント ベースの認証と証明書ベースの認証の違い)]」を参照してください。

アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

### ログインによる方法の使用

組織のアカウントを使用してログインするには、次のコマンドを使用します。

    azure login [username] [password]

> [WACOM.NOTE] 初めてこれらの資格情報を使用してログインする場合は、認証トークンをキャッシュするかどうかの確認を求められます。このプロンプトは、前に説明している `azure logout` コマンドを使用した場合にも表示されます。自動化のシナリオでこのプロンプトが表示されないようにするには、`-q` パラメーターを付けて `azure login` コマンドを使用してください。
>
> 組織のアカウントで認証されたら、Azure サブスクリプションにアクセスするための情報が `user` ディレクトリの `.azure` ディレクトリに格納されます。`user` ディレクトリはシステムによって保護されますが、追加の作業を行って `user` ディレクトリを暗号化することをお勧めします。そのためには、次の操作を行います。

ログアウトするには、次のコマンドを使用します。

    azure logout [username]

> [WACOM.NOTE] アカウントに関連付けられたサブスクリプションが Active Directory のみを使用して認証された場合は、ログアウトするとローカル プロファイルからサブスクリプション情報が削除されます。ただし、サブスクリプション用に発行設定ファイルもインポートされている場合は、ログアウトするとローカル プロファイルから Active Directory 関連の情報のみが削除されます。

> [WACOM.NOTE] アカウントを使用した認証では、次のコマンドは正しく機能しません。
>
> -   `azure vm`
> -   `azure network`
> -   `azure mobile`
>
> これらのコマンドを使用する必要がある場合は、次のセクションで説明しているように、発行設定ファイルを使用して Azure に対して認証する必要があります。

### 発行設定ファイルによる方法の使用

アカウントの発行設定をダウンロードするには、次のコマンドを使用します。

    azure account download

既定のブラウザーが開き、Azure 管理ポータルにサインインするよう促されます。サインインすると、`.publishsettings` ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

> [WACOM.NOTE] アカウントが複数の Azure Active Directory (AD) テナントに関連付けられている場合は、発行設定ファイルのダウンロード対象となる Active Directory を選択するよう促されることがあります。
>
> ダウンロード ページを使用するか、Azure の管理ポータルにアクセスして選択を行った後、選択した Active Directory は、ポータルおよびダウンロード ページによって使用される既定になります。既定を確定した後、'**click here to return to the selection page (選択ページに戻るにはここをクリックしてください)**' というテキストが、ダウンロード ページの上部に表示されます。選択されたページに戻るには、用意されているリンクを使用します。

次に、次のコマンドを実行して `.publishsettings` ファイルをインポートします。ここで、`[path to .publishsettings file]` には、`.publishsettings` ファイルのパスを指定してください。

    azure account import [path to .publishsettings file]

> 発行設定をインポートすると、Azure サブスクリプションにアクセスするための情報が、`user` ディレクトリの `.azure` ディレクトリに格納されます。`user` ディレクトリはシステムによって保護されますが、追加の作業を行って `user` ディレクトリを暗号化することをお勧めします。そのためには、次の操作を行います。
>
> -   Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。
> -   Mac の場合、ディレクトリに対して FileVault を有効にします。
> -   Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同等の機能が用意されています。

発行設定をインポートしたら、`.publishsettings` ファイルを削除します。このファイルはコマンド ライン ツールでは不要であり、第三者によってサブスクリプションヘのアクセスに使用されるセキュリティ上のリスクがあるためです。

### 複数のサブスクリプション

複数の Azure サブスクリプションがある場合は、ログインすると、資格情報に関連付けられているすべてのサブスクリプションへのアクセスが許可されます。発行設定ファイルを使用する場合、`.publishsettings` ファイルには全サブスクリプションの情報が含まれます。いずれの方法でも、操作の実行時に xplat-cli によって使用される既定のサブスクリプションとして 1 つのサブスクリプションが選択されます。`azure account list` コマンドを使用すると、すべてのサブスクリプションおよび既定のサブスクリプションを表示できます。このコマンドでは、次のような情報が返されます。

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

前のリストでは、**Current** 列に現在の既定のサブスクリプションが表示されています (この場合は Azure-sub-1)。既定のサブスクリプションを変更するには、`azure account set` コマンドを使用して、既定にするサブスクリプションを指定します。次に例を示します。

    azure account set Azure-sub-2

この結果、既定のサブスクリプショは Azure-sub-2 に変更されます。

> [WACOM.NOTE] 既定のサブスクリプションの変更はすぐに有効になり、グローバル設定です。新しい xplat-cli コマンドの実行には、同じコマンドライン インスタンスからでも別のインスタンスからでも、この新しい既定のサブスクリプションが使用されます。

xplat-cli で、既定のサブスクリプションを変更せずに既定ではないサブスクリプションを使用する場合は、`--subscription` オプションを使用して、その操作に使用するサブスクリプションの名前を指定します。

## <span id="use"></span></a>方法: Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する

xplat-cli には `azure` コマンドを使用してアクセスします。使用できるコマンドの一覧を表示するには、`azure` コマンドをパラメーターなしで使用します。次のようなヘルプ情報が表示されます。

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

ここに表示されているトップ レベルのコマンドには、Azure の特定の領域で機能するコマンドが含まれています。たとえば、`azure account` コマンドには、以前使用した `download` や `import` 設定など、Azure サブスクリプションに関連するコマンドが含まれています。

ほとんどのコマンドは `azure <command> <operation> [parameters]` という形式で、サービスやアカウント構成などのオブジェクトに対する操作を行います。その他のコマンドにはサブコマンドがあり、`azure <command> <subcommand> <operation> [parameters]` という形式で指定します。次のコマンド例は、アカウント構成に対する操作です。

-   インポート済みのサブスクリプションを表示する。

        azure account list

-   複数のサブスクリプションをインポート済みで、1 つのサブスクリプションを既定に設定する。

        azure account set <subscription>

`--help` または `-h` パラメーターを使用すると特定のコマンドに対するヘルプを表示できます。または、`azure help [command] [options]` の形式で指定しても、同じ情報が返されます。たとえば、次のコマンドでは、すべて同じ情報が返されます。

    azure account set --help

    azure account set -h

    azure help account set

コマンドに必要なパラメーターがわからない場合は `--help`、`-h`、または `azure help [command]` を使用してヘルプを参照してください。

### 構成モードの設定

xplat-cli では、データベース サーバー、データベース、Web サイトなど、ユーザー管理のエンティティである個々の *resources* に対して管理操作を実行できます。これは xplat-cli の既定の動作モードであり、**Azure サービス管理**と呼ばれています。ただし、複数のリソースで構成された複雑なソリューションがあるときは、ソリューション全体を 1 つの論理単位として管理できるようにすると便利です。

1 つの論理単位 (*resource group*) としてリソースのグループを管理できるように、Azure のリソースを管理する新しい方法として、**リソース マネージャー**のプレビュー版が導入されています。

> [WACOM.NOTE] リソース マネージャーは、現在プレビュー版であり、Azure サービス管理と同じレベルの管理機能は提供されていません。

新しいリソース マネージャーをサポートするために、xplat-cli では、`azure config mode` コマンドを使用して、これらの管理モードを切り替えることができます。

xplat-cli では、Azure サービス管理モードに既定で設定されています。リソース マネージャー モードに切り替えるには、次のコマンドを使用します。

    azure config mode arm

Azure サービス管理モードに戻すには、次のコマンドを使用します。

    azure config mode asm 

> [WACOM.NOTE] リソース マネージャー モードと Azure サービス管理モードは互いに排他的です。つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。

xplat-cli を使用したリソース マネージャーの操作の詳細については、「[リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用][リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用]」を参照してください。

### Azure サービス管理モードでのサービスの操作

xplat-cli を使用すると、Azure サービスを簡単に管理できます。この例では、xplat-cli を使用して Azure Web サイトを管理する方法について説明します。

1.  新しい Azure Web サイトを作成するには、次のコマンドを使用します。**mywebsite** は、一意の名前に置き換えてください。

        azure site create mywebsite

    Web サイトが作成される場所を指定するように求められます。地理的に近いリージョンを選択します。コマンドを実行すると、Web サイトが <http://mywebsite.azurewebsites.net> で使用できるようになります (**mywebsite** は、作成時に指定した名前に置き換えます)。

    > [WACOM.NOTE] プロジェクト ソース管理に Git を使用する場合、`--git` パラメーターを指定して、この Web サイト用の Git リポジトリを Azure に作成します。Git リポジトリがないディレクトリからこのコマンドを実行した場合、Git リポジトリの再初期化も行われます。また、**azure** という名前の Git リモートも作成されます。これは、`git push azure master` コマンドを使用して Azure Web サイトに展開をプッシュするために使用できます。

    > [WACOM.NOTE] 'site' が Azure のコマンドではないというエラーが発生した場合、高い確率で xplat-cli がリソース グループ モードになっています。リソース モードに戻すには、`azure config mode asm` コマンドを使用してください。

2.  サブスクリプションのすべての Web サイトを表示するには、次のコマンドを使用します。

        azure site list

    このリストには、前の手順で作成したサイトが含まれます。

3.  Web サイトを停止するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

        azure site stop mywebsite

    コマンドが完了したら、ブラウザーを更新するとサイトが停止したことを確認できます。

4.  Web サイトを開始するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

        azure site start mywebsite

    コマンドが完了したら、ブラウザーを更新するとサイトが開始したことを確認できます。

5.  Web サイトを削除するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

        azure site delete mywebsite

    コマンドが完了したら、`azure site list` コマンドを使用して、Web サイトが削除されていることを確認します。

## <span id="script"></span></a>方法: Azure クロス プラットフォーム コマンド ライン インターフェイスでスクリプトを使用する

xplat-cli を使用して手動でコマンドを発行できますが、お使いのシステムで使用できるコマンド ライン インタープリターやその他のコマンド ライン ユーティリティの機能を利用して、複雑な自動化ワークフローを作成することもできます。たとえば、次のコマンドはすべての Azure Web サイトを停止します。

    azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

この例では、`grep` コマンドに Web サイトのリストをパイプして、文字列 "Running" について各行を調べています。一致した行は `awk` コマンドにパイプされます。`azure site stop` が呼び出され、渡された 2 番目の列 (実行中のサイト名) が、停止するサイト名に使用されます。

これは、コマンドの連結方法を示した例ですが、コマンド ライン インタープリターのスクリプト機能を使用して、さらに複雑なスクリプトを作成することもできます。コマンドライン インタープリターの種類ごとに、スクリプト機能や構文が異なります。Linux および OS X を含め、UNIX ベースのシステム用には Bash が最も広く使用されているコマンド ライン インタープリターでしょう。

Bash でのスクリプトの作成方法については、「[Advanced Bash-Scripting Guide][Advanced Bash-Scripting Guide]」を参照してください。

OS X または Linux ベース システムのスクリプトの作成の全般的な情報については、「[Shell script][Shell script]」を参照してください。

バッチ ファイルを使用した Windows ベース システムのスクリプトについては、[「Command-line reference A-Z」][「Command-line reference A-Z」]を参照してください。

### 結果について

スクリプトの作成時、コマンド出力をキャプチャしたり、出力を別のコマンドに渡したり、出力に特別な操作 (特定の値の確認など) を実行したりすることがあります。xplat-cli は出力を STDOUT および STDERR に生成します。各行にはプレフィックスとして、情報ステータス メッセージの場合は文字列 `info:` が、サービスに関して返されるデータの場合は `data:` がそれぞれ付加されますが、`--verbose` または `-v` パラメーターを使用すると、より詳細な情報を返すよう xplat-cli に指示できます。これらのパラメーターで返される追加の情報には、文字列 `verbose:` がプレフィックスとして付加されます。

たとえば、次の出力は `azure site list` コマンドで返された情報です。

    info:    Executing command site list
    + Enumerating sites
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

`--verbose` または `-v` パラメーターを指定すると、次のような情報が返されます。

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

`verbose:` の情報は、JSON 形式のデータです。[jsawk][jsawk] や [jq][jq] など、JSON をネイティブで認識するユーティリティを使用している場合は、`--json` パラメーターを使用して、情報を JSON 形式で取得できます。次に例を示します。

    azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

このコマンドは Web サイトのリストを JSON として取得してから jsawk を使用してサイト名を取得し、最後に xargs を使用して、各サイトの削除コマンドを実行します。このサイト名はパラメーターとして渡されています。

> [WACOM.NOTE] `--json` パラメーターは、ステータスの生成またはデータ情報をブロックします (プレフィックス `info:` および `data:` がある文字列)。たとえば、`--json` パラメーターを `azure site create` コマンドで使用した場合、返される出力はありません。このコマンドでは `info:` 以外のデータを返さないためです。

### エラーの操作

xplat-cli では、エラー情報を STDERR にログ出力しますが、エラーに関する追加情報が、スクリプトを実行したディレクトリにある **azure.err** ファイルに記録されます。このファイルに情報が記録された場合、次のような文字列が STDOUT に書き込まれます。

    info:    Error information has been recorded to azure.err

### 終了ステータス

必須パラメーターが不足している場合、一部の xplat-cli コマンドは 0 以外の終了ステータスを返しません。代わりに、ユーザーの入力が要求されます。たとえば、Web サイトを新規作成する `azure site create` コマンドを使用したとき、名前または `--location` パラメーターがない場合は、これらの値を指定するように求められます。

終了ステータスに依存するスクリプトを作成する場合は、使用する xplat-cli コマンドがユーザー入力を求めないことを確認してください。

## <span id="additional-resources"></span></a>その他のリソース

-   ソース コードのダウンロード、問題の報告、プロジェクトへの協力など、xplat-cli の詳細については、「<a href="https://github.com/WindowsAzure/azure-sdk-tools-xplat">Azure クロス プラットフォーム コマンド ライン インターフェイスの GitHub リポジトリ</a>」を参照してください。

-   xplat-cli または Azure の使用で問題が発生した場合は、[Azure フォーラム][Azure フォーラム]にアクセスしてください。

-   Azure の詳細については、[][2]<http://azure.microsoft.com/></a> を参照してください。

  []: https://github.com/WindowsAzure/azure-sdk-tools-xplat
  [方法: Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする]: #install
  [方法: Azure サブスクリプションに接続する]: #configure
  [方法: Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する]: #use
  [方法: Azure クロス プラットフォーム コマンド ライン インターフェイスでスクリプトを使用する]: #script
  [その他のリソース]: #additional-resources
  [1]: http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409
  [Windows インストーラー]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
  [OS X インストーラー]: http://go.microsoft.com/fwlink/?LinkId=252249
  [Azure の管理ポータル]: https://manage.windowsazure.com
  [Microsoft Azure への組織としてのサインアップ]: http://www.windowsazure.com/ja-jp/documentation/articles/sign-up-organization/
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A7171371E
  [リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用]: /ja-jp/documentation/articles/xplat-cli-azure-resource-manager/
  [Advanced Bash-Scripting Guide]: http://tldp.org/LDP/abs/html/
  [Shell script]: http://en.wikipedia.org/wiki/Shell_script
  [「Command-line reference A-Z」]: http://technet.microsoft.com/ja-jp/library/bb490890.aspx
  [jsawk]: https://github.com/micha/jsawk
  [jq]: http://stedolan.github.io/jq/
  [Azure フォーラム]: http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home
  [2]: http://azure.microsoft.com

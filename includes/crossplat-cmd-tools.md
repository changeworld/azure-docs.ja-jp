# Mac および Linux 用 Azure コマンド ライン ツールの使用方法

このガイドでは、Mac および Linux 用 Azure コマンド ライン ツールを使用して Azure のサービスを作成および管理する方法について説明します。ここでは、**ツールのインストール**、**発行の設定のインポート**、**Azure の Web サイトの作成および管理**、**Azure の仮想マシンの作成および管理**の各シナリオについて説明します。包括的なリファレンス ドキュメントについては、[Mac および Linux 用 Azure コマンド ライン ツールに関するページ][Mac および Linux 用 Azure コマンド ライン ツールに関するページ]を参照してください。

## 目次

-   [Mac および Linux 用 Azure コマンド ライン ツールについて][Mac および Linux 用 Azure コマンド ライン ツールについて]
-   [Mac および Linux 用 Azure コマンド ライン ツールのインストール方法][Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]
-   [Azure アカウントの作成方法][Azure アカウントの作成方法]
-   [発行の設定をダウンロードおよびインポートする方法][発行の設定をダウンロードおよびインポートする方法]
-   [Azure の Web サイトを作成および管理する方法][Azure の Web サイトを作成および管理する方法]
-   [Azure の仮想マシンを作成および管理する方法][Azure の仮想マシンを作成および管理する方法]

## <span id="Overview"></span></a>Mac および Linux 用 Azure コマンド ライン ツールについて

Mac および Linux 用 Azure コマンド ライン ツールは、Azure サービスをデプロイおよび管理するためのコマンド ライン ツールのセットです。

次のようなタスクがサポートされます。

-   発行の設定のインポート。
-   Azure の Web サイトの作成および管理。
-   Azure の仮想マシンの作成および管理。

サポートされているコマンドの完全な一覧については、ツールをインストールした後コマンド ラインで「`azure -help`」と入力するか、または[リファレンス ドキュメント][Mac および Linux 用 Azure コマンド ライン ツールに関するページ]を参照してください。

## <span id="Download"></span>Mac および Linux 用 Azure コマンド ライン ツールのインストール方法</a>

次の説明に従って、お使いのオペレーティング システムに対応するコマンド ライン ツールをインストールしてください。

-   **Mac**: [Azure SDK インストーラー][Azure SDK インストーラー]をダウンロードします。ダウンロードした .pkg ファイルを開き、指示に従ってインストール手順を実行します。

-   **Linux**: 最新バージョンの [Node.js][Node.js] をインストールし ([パッケージ マネージャーを使った Node.js のインストールに関するページ][パッケージ マネージャーを使った Node.js のインストールに関するページ]を参照)、次のコマンドを実行します。

        npm install azure-cli -g

    **メモ**:昇格した特権で次のコマンドを実行することが必要になる場合があります。

        sudo npm install azure-cli -g

-   **Windows**: Windows インストーラー (.msi ファイル) を実行します。このファイルは、[Azure コマンド ライン ツール][Azure コマンド ライン ツール]から入手できます。

インストールをテストするには、コマンド プロンプトで「`azure`」と入力します。インストールが正常に完了している場合は、使用可能なすべての `azure` コマンドの一覧が表示されます。

## <span id="CreateAccount"></span></a>Azure アカウントの作成方法

Mac および Linux 用 Azure コマンド ライン ツールを使用するには、Azure アカウントが必要です。

Web ブラウザーを開き、[][]<http://www.windowsazure.com></a> にアクセスします。右上隅に表示された **[無料評価版]** をクリックします。

![Azure Web Site][Azure Web Site]

指示に従ってアカウントの作成手順を実行します。

## <span id="Account"></span></a>発行の設定をダウンロードおよびインポートする方法

最初に、発行の設定をダウンロードしてインポートする必要があります。これにより、Azure サービスを作成および管理するためのツールを使用できるようになります。発行の設定をダウンロードするには、次のように `account download` コマンドを使用します。

    azure account download

既定のブラウザーが開き、管理ポータルにサインインするよう促されます。サインインすると、`.publishsettings` ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

次に、次のコマンドを実行して `.publishsettings` ファイルをインポートします。ここで、`{path to .publishsettings file}` には、`.publishsettings` ファイルのパスを指定してください。

    azure account import {path to .publishsettings file}

`account clear` コマンドを使用すると、`import` コマンドによって格納されたすべての情報を削除できます。

    azure account clear

`account` コマンドのオプションの一覧を表示するには、`-help` オプションを使用します。

    azure account -help

発行設定をインポートした後は、セキュリティ上の理由から、`.publishsettings` ファイルを削除する必要があります。

<div class="dev-callout"> 
<b>注</b> 
<p>発行の設定をインポートすると、Azure サブスクリプションにアクセスするための資格情報が <code data-inline="1">user</code> フォルダーに格納されます。<code data-inline="1">user</code> フォルダーはオペレーティング システムによって保護されていますが、追加の作業を行って <code data-inline="1">user</code> フォルダーを暗号化することをお勧めします。そのためには、次の操作を行います。</p>

<ul>
<li>Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。</li>
<li>Mac の場合、フォルダーに対して FileVault を有効にします。</li>
<li>Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同等の機能が用意されています。</li>
</ul>

</div>

これで、Azure の Web サイトおよび Azure の仮想マシンを作成および管理する準備が整いました。

## <span id="WebSites"></span></a>Azure の Web サイトを作成および管理する方法

### Web サイトの作成

Azure の Web サイトを作成するには、最初に `MySite` という空のディレクトリを作成し、そのディレクトリを参照します。

次に、次のコマンドを実行します。

    azure site create MySite --git

このコマンドの出力には、新しく作成された Web サイトの既定 URL が含まれています。`--git` オプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

`azure site create` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

-   `--location [location name]`。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (たとえば、"米国西部")。このオプションを省略した場合、場所を選択するよう促されます。
-   `--hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。

次に、Web サイト ディレクトリにコンテンツを追加します。通常の git フロー (`git add`、`git commit`) を使用してコンテンツをコミットします。次の git コマンドを使用して、Web サイトのコンテンツを Azure にプッシュします。

    git push azure master

### GitHub からの発行の設定

GitHub リポジトリからの継続的な発行を設定するには、サイトの作成時に `--GitHub` オプションを使用します。

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

GitHub リポジトリのローカル クローンがある場合や、GitHub リポジトリへの単一のリモート参照を含むリポジトリがある場合、このコマンドを実行すると GitHub リポジトリ内のコードがサイトに自動的に発行されます。それ以降、GitHub リポジトリにプッシュされたすべての変更がサイトに自動的に発行されます。

GitHub からの発行を設定すると、master 分岐が既定の分岐として使用されます。別の分岐を指定するには、ローカル リポジトリから次のコマンドを実行します。

    azure site repository <branch name>

### アプリケーションの設定の構成

アプリケーション設定は、実行時にアプリケーションで使用できるキーと値のペアです。Azure の Web サイトに設定すると、アプリケーションの設定の値により、サイトの Web.config ファイルで定義されたのと同じキーで設定が上書きされます。Node.js アプリケーションと PHP アプリケーションの場合、アプリケーション設定を環境変数として使用できます。次の例は、キーと値のペアを設定する方法を示しています。

    azure site config add <key>=<value> 

すべてのキー/値ペアの一覧を参照するには、次のコマンドを使用します。

    azure site config list 

または、キーがわかっていて、値を参照する場合は、次のコマンドを使用できます。

    azure site config get <key> 

既存のキーの値を変更する場合は、まず既存のキーをクリアして再度追加します。クリア コマンドは次のとおりです。

    azure site config clear <key> 

### サイトの一覧表示と表示

Web サイトの一覧を表示するには、次のコマンドを使用します。

    azure site list

サイトに関する詳細な情報を取得するには、`site show` コマンドを使用します。次の例は、`MySite` に関する詳細な情報を表示します。

    azure site show MySite

### サイトの停止、開始、または再起動

サイトを停止、開始、再起動するには、`site stop`、`site start`、`site restart` の各コマンドを使用します。

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### サイトの削除

最後に、サイトを削除するには、`site delete` コマンドを使用します。

    azure site delete MySite

上記のコマンドを `site create` を実行したフォルダー内で実行する場合は、サイト名 `MySite` を最終パラメーターとして指定する必要はありません。

`site` コマンドの完全な一覧を表示するには、`-help` オプションを使用します。

    azure site -help 

## <span id="VMs"></span></a>Azure の仮想マシンを作成および管理する方法

Azure の仮想マシンを作成するには、自分で用意した仮想マシン イメージ (.vhd ファイル) やイメージ ギャラリーから入手した仮想マシン イメージを使用します。利用可能なイメージを表示するには、`vm image list` コマンドを使用します。

    azure vm image list

使用可能なイメージの 1 つから仮想マシンをプロビジョニングおよび起動するには、`vm create` コマンドを使用します。次の例に、イメージ ギャラリー内のイメージ (CentOS 6.2) から Linux 仮想マシン (`myVM`) を作成する方法を示します。仮想マシンのルート ユーザー名とパスワードは、それぞれ `myusername` と `Mypassw0rd` です (`--location` パラメーターは、仮想マシンが作成されるデータ センターを指定します。`--location` パラメーターを省略した場合、場所の選択を求められます)。

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-ja-jp-30GB.vhd myusername --location "West US"

`--ssh` フラグ (Linux) または `--rdp` フラグ (Windows) を `vm create` に渡すと、新しく作成された仮想マシンへのリモート接続が有効になります。

仮想マシンをカスタム イメージからプロビジョニングする場合は、`vm image create` コマンドを使用して .vhd ファイルからイメージを作成し、`vm create` コマンドを使用して仮想マシンをプロビジョニングします。次の例に、ローカル .vhd ファイルから Linux イメージ (`myImage`) を作成する方法を示します (`--location` パラメーターは、イメージが格納されるデータを指定します)。

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

イメージをローカル .vhd から作成する代わりに、Azure BLOB ストレージに格納されている .vhd からイメージを作成できます。そのためには、`blob-url` パラメーターを使用します。

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

イメージを作成した後は、`vm create` を使用して仮想マシンをプロビジョニングできます。次のコマンドは、上の手順で作成したイメージ (`myImage`) から仮想マシン `myVM` を作成します。

    azure vm create myVM myImage myusername --location "West US"

仮想マシンをプロビジョニングした後は、たとえば、エンドポイントを作成して、仮想マシンにリモート アクセスできるようにすることができます。次の例では、`vm create endpoint` コマンドを使用して、`myVM` の外部ポート 22 とローカル ポート 22 を開いています。

    azure vm endpoint create myVM 22 22

仮想マシンに関する詳細な情報 (IP アドレス、DNS 名、エンドポイント情報) を取得するには、`vm show` コマンドを使用します。

    azure vm show myVM

仮想マシンをシャットダウン、起動、または再起動するには、次のいずれかのコマンドを使用します。

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

最後に、VM を削除するには、`vm delete` コマンドを使用します。

    azure vm delete myVM

仮想マシンを作成および管理するためのコマンドの一覧を表示するには、`-h` オプションを使用します。

    azure vm -h

<!-- IMAGES --> 
<!-- LINKS -->

  [Mac および Linux 用 Azure コマンド ライン ツールに関するページ]: http://go.microsoft.com/fwlink/?LinkId=252246
  [Mac および Linux 用 Azure コマンド ライン ツールについて]: #Overview
  [Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]: #Download
  [Azure アカウントの作成方法]: #CreateAccount
  [発行の設定をダウンロードおよびインポートする方法]: #Account
  [Azure の Web サイトを作成および管理する方法]: #WebSites
  [Azure の仮想マシンを作成および管理する方法]: #VMs
  [Azure SDK インストーラー]: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [パッケージ マネージャーを使った Node.js のインストールに関するページ]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Azure コマンド ライン ツール]: http://go.microsoft.com/fwlink/?LinkID=275464
  []: http://www.windowsazure.com
  [Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

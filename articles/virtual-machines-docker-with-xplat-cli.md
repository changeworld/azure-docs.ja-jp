<properties title="Azure クロスプラットフォーム インターフェイス (xplat-cli) から Docker VM 拡張機能を使用する方法" pageTitle="Azure での Linux 用 Docker VM 拡張機能の使用" description="Docker と Azure 仮想マシン拡張機能について説明し、azure-cli コマンド インターフェイスを使用してコマンド ラインから Azure 上に Docker ホストである仮想マシンをプログラムで作成する方法を示します。" metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />

# Azure クロスプラットフォーム インターフェイス (xplat-cli) からの Docker VM 拡張機能の使用

このトピックでは、任意のプラットフォーム上の xplat-cli から Docker VM 拡張機能を使用して VM を作成する方法について説明します。[Docker][Docker] は、最もよく利用されている仮想化アプローチの 1 つで、データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー][Linux コンテナー]を使用します。[Azure Linux エージェント][Azure Linux エージェント]に対して Docker VM 拡張機能を使用すれば、Azure 上に Docker VM を作成し、アプリケーション用に任意の数のコンテナーをホストさせることができます。コンテナーとその利点に関する概要については、「[Docker High Level Whiteboard (Docker の概要ホワイトボード)][Docker High Level Whiteboard (Docker の概要ホワイトボード)]」を参照してください。

-   [Azure で Docker VM 拡張機能を使用する方法][Azure で Docker VM 拡張機能を使用する方法]
-   [Linux および Windows 向けの仮想マシン拡張機能][Linux および Windows 向けの仮想マシン拡張機能]
-   [Azure 向けのコンテナーおよびコンテナー管理リソース][Azure 向けのコンテナーおよびコンテナー管理リソース]
-   [次のステップ][次のステップ]

## <span id="How to use the Docker VM Extension with Azure"></span>Azure で Docker VM 拡張機能を使用する方法</a>

Azure で Docker VM 拡張機能を使用するには、バージョン 0.8.6 以降の [Azure クロスプラットフォーム コマンド ライン インターフェイス][Azure クロスプラットフォーム コマンド ライン インターフェイス] (**xplat-cli** と呼びます) をインストールする必要があります (この記事の執筆時点で最新バージョンは 0.8.10 です)。xplat-cli は Mac、Linux、Windows のいずれにもインストールできます。

> [WACOM.NOTE] xplat-cli は Microsoft Windows にインストールできますが、Docker は Linux に固有のカーネル依存関係を使用して構築されています。そのため、Windows を Docker クライアントとして使用するには、Linux のフル ディストリビューションを Hyper-V などのハイパーバイザー内に仮想マシンとしてホストする必要があります。これを実行すると、この記事や Docker ドキュメントに記載されている xplat-cli と Docker のコマンドを使用することができます。Docker でも [Boot2Docker][Boot2Docker] という Windows 用のセットアップ プログラムが提供されており、これを使用して同じセットアップを自動で実行できます。

Azure 上で Docker を使用するためのプロセスはシンプルです。

-   xplat-cli コマンド ライン ツールとその依存関係を、Azure を制御するコンピューターにインストールする (Windows の場合は、仮想マシンとして実行される Linux ディストリビューション)。
-   xplat-cli Docker コマンドを使用して、Azure に VM Docker ホストを作成する。
-   ローカルの Docker コマンドを使用して、Azure 上の Docker VM 内の Docker コンテナーを管理する。

> [WACOM.NOTE] xplat-cli (コマンド ライン インターフェイス) は、現時点で、Azure 上に Docker 制御型 VM を作成して Docker コンテナーをホストする唯一の手段です。

### クロスプラットフォーム コマンド ライン インターフェイス (xplat-cli) のインストール

クロスプラットフォーム コマンドライン インターフェイスのインストールと構成については、「[方法: Azure クロスプラットフォーム コマンド ライン インターフェイスをインストールする][方法: Azure クロスプラットフォーム コマンド ライン インターフェイスをインストールする]」を参照してください。インストールを確認するために、コマンド プロンプトで「`azure`」と入力します。しばらくすると、xplat-cli の ASCII アートが表示され、使用可能な基本コマンドが一覧表示されます。インストールが正しく機能していれば、「`azure help vm`」と入力したときに、一覧表示されたコマンドに "docker" が含まれています。

> [WACOM.NOTE] Ubuntu 14.04 LTS インストールを使用する場合は、イメージのノード インストールが若干異なっているため、追加作業が必要になる場合があります。この作業に役立ちそうな提案事項が、[こちら][こちら]の「**How To Install Using a PPA (PPA を使用してインストールする方法)**」セクションに記載されています。この記事では、nodejs の最新バージョンを直接インストールする方法が説明されており、Ubuntu 14.04 LTS ディストリビューションにも問題なく適用できるようです。

### Azure アカウントへの xplat-cli の接続

xplat-cli を使用する前に、プラットフォーム上で Azure アカウントの資格情報を xplat-cli に関連付ける必要があります。セクション「[方法: Azure サブスクリプションに接続する][方法: Azure サブスクリプションに接続する]」に、**.publishsettings** ファイルをダウンロードしてインポートする方法と xplat-cli コマンド ラインを組織 ID に関連付ける方法が説明されています。

> [WACOM.NOTE] 使用する認証方法によって動作に若干の違いがあるため、必ず前述のドキュメントをよく読んで、機能の違いを理解してください。

### Docker のインストールと Azure 用 Docker VM 拡張機能の使用

続いては、[Docker のインストール手順][Docker のインストール手順]に従って、Docker をコンピューターにローカルにインストールします。ほとんどのオペレーティング システムやディストリビューションでは、「`apt-get install docker.io`」と入力します。Docker のバージョンが 1.0 以降であることを確認してください。

Azure 仮想マシンで Docker を使用するには、VM に使用する Linux イメージに [Azure Linux VM エージェント][Azure Linux エージェント]がインストールされている必要があります。現在、このエージェントを提供するイメージは次の 2 種類のみです。

-   Azure イメージ ギャラリーで入手できる Ubuntu イメージ

-   自分で作成し、Azure Linux VM エージェントをインストールして構成したカスタム Linux イメージ。カスタム Linux VM イメージを作成し、Azure VM エージェントをインストールする方法については、「[Azure Linux エージェント ユーザー ガイド][Azure Linux エージェント]」を参照してください。

### Azure イメージ ギャラリーの使用

bash またはターミナル セッションから、次の xplat-cli コマンドを入力して、VM ギャラリーで最新の Ubuntu イメージを見つけます。

`azure vm image list | grep Ubuntu-14_04`

`b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-ja-jp-30GB` など、イメージ名のいずれかを選び、次のコマンドを入力して、そのイメージを使用した新しい VM を作成します。

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-ja-jp-30GB" <username> <password>

各値の説明:

-   *\<vm-cloudservice name\>* は、Azure で Docker コンテナーのホスト コンピューターになる VM の名前です。

-   *\<username\>* は、VM の既定の root ユーザーのユーザー名です。

-   *\<password\>* は、Azure の複雑さの基準を満たした *username* アカウントのパスワードです。

> [WACOM.NOTE] 現時点では、パスワードは 8 文字以上で、小文字、大文字、数字、および特殊文字 (`!@#$%^&+=` のいずれか) を含める必要があります。

コマンドが正常に実行されると、使用した引数とオプションに応じて、以下のような応答が表示されます。

![][0]

> [WACOM.NOTE] 仮想マシンの作成には数分かかることがありますが、プロビジョニングが終わると、Docker デーモン (Docker サービス) が起動し、Docker コンテナー ホストに接続できるようになります。

Azure 上に作成した Docker VM をテストするために、次のコマンドを入力します。

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

*<vm-name-you-used>* は、`azure vm docker create` の呼び出しで使用した仮想マシンの名前です。以下のような応答が表示されます。Azure 上で Docker ホスト VM が稼働し、コマンドの入力を待機していることが示されています。

![][1]

### Docker ホスト VM の認証

コマンド `azure vm docker create` では、Docker VM の作成に加えて、必要な証明書も自動的に作成されます。Docker クライアント コンピューターは、この証明書を使用することで、HTTPS を介して Azure コンテナー ホストに接続できます。証明書は必要に応じてクライアントとホスト マシンのどちらにも格納されます。以降の実行では、既存の証明書が再利用され、新しいホストと共有されます。

既定では、証明書は `~/.docker` に配置されます。Docker はポート **4243** で実行するように構成されます。別のポートやディレクトリを使用する場合は、以下の `azure vm docker create` コマンド ライン オプションのいずれかを使用して、Docker コンテナー ホスト VM がクライアントとの接続に別のポートや証明書を使用するよう構成できます。

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

ホストの Docker デーモンは、`azure vm docker create` コマンドで生成された証明書を使用して、指定されたポートでクライアント接続をリッスンし、認証するよう構成されています。クライアント コンピューターには、Docker ホストにアクセスできるように、これらの証明書が配置されている必要があります。

> [WACOM.NOTE] ネットワークに接続されたホストにこれらの証明書がない場合は、コンピューターに接続できる第三者に対して脆弱になります。既定の構成を変更する前に、コンピューターやアプリケーションに対するリスクについてよく理解してください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 次のステップ

「[Docker ユーザー ガイド][Docker ユーザー ガイド]」にアクセスして Docker VM の使用を開始する準備が整いました。新しいポータルで Docker 対応 VM を作成する方法については、「[Using the Docker VM Extension with the Azure Portal (Azure ポータルでの Docker VM 拡張機能の使用)][Using the Docker VM Extension with the Azure Portal (Azure ポータルでの Docker VM 拡張機能の使用)]」を参照してください。

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [Docker]: https://www.docker.com/
  [Linux コンテナー]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux エージェント]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-linux-agent-user-guide/
  [Docker High Level Whiteboard (Docker の概要ホワイトボード)]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Azure で Docker VM 拡張機能を使用する方法]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Linux および Windows 向けの仮想マシン拡張機能]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Azure 向けのコンテナーおよびコンテナー管理リソース]: #Container-and-Container-Management-Resources-for-Azure
  [次のステップ]: #next-steps
  [Azure クロスプラットフォーム コマンド ライン インターフェイス]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [方法: Azure クロスプラットフォーム コマンド ライン インターフェイスをインストールする]: http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/#install
  [こちら]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [方法: Azure サブスクリプションに接続する]: http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/#configure
  [Docker のインストール手順]: https://docs.docker.com/installation/#installation
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [1]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Docker ユーザー ガイド]: https://docs.docker.com/userguide/
  [Using the Docker VM Extension with the Azure Portal (Azure ポータルでの Docker VM 拡張機能の使用)]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-docker-with-portal/

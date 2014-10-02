<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com"></tags>

# Azure で Linux 用 Docker 仮想マシン拡張機能を使用する方法

[Docker][] は、最もよく利用されている仮想化アプローチの 1 つで、データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー][]を使用します。[Azure Linux エージェント][]に対して Docker VM 拡張機能を使用すれば、Azure 上に Docker VM を作成し、アプリケーション用に任意の数のコンテナーをホストさせることができます。

このトピックでは、こちらの [MS Open Tech ブログのお知らせ][]に基づいて、次の点について説明します。

-   [Docker と Linux コンテナー][]
-   [Azure で Docker VM 拡張機能を使用する方法][]
-   [Linux および Windows 向けの仮想マシン拡張機能][]
-   [Azure 向けのコンテナーおよびコンテナー管理リソース][]

## Docker と Linux コンテナー

[Docker][] は、最もよく利用されている仮想化アプローチの 1 つで、データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー][]を使用します。また、Docker が提供しているサービスを利用して、アプリケーションを短時間で構築、アセンブルし、他の Docker コンテナーに配布することもできます。

Docker と Linux コンテナーは、Windows Hyper-V や Linux の [KVM][] のような (他にも例は多数あります) [ハイパーバイザー][]ではありません。ハイパーバイザーでは基になるオペレーティング システムが仮想化されるため、オペレーティング システム全体がハイパーバイザー内でアプリケーションのように実行されます。

ハイパーバイザー アプローチは、"ゲスト" 仮想マシンが "ホスト" オペレーティング システムにアクセスせず、ハイパーバイザーのリソースしか使用しないため、セキュリティ面で大きなメリットがあります。ただし、デメリットもあり、処理とストレージのオーバーヘッドが大きく、新しい仮想マシンの起動に比較的時間がかかります。この理由はいくつかありますが、ゲスト オペレーティング システムが完全にレプリケートされることが主な原因です。

#### Docker と Linux コンテナー

Docker をはじめとする*コンテナー* アプローチでは、Linux カーネルが備えるプロセスとファイル システムの分離機能を使用し、カーネル機能の利用以外の部分では互いに分離されたコンテナーを構築することで、起動時間と処理やストレージのオーバーヘッドを大幅に軽減しています。コンテナーの内部では、実行されているアプリケーションがファイル システムとカーネル機能を使用する唯一のアプリケーションであるかのように見えます。

また、Docker では複数のコンテナー管理機能が提供されており、コミュニティから提供されるさまざまなコンテナー イメージを読み込んだり、短時間で独自のコンテナー イメージを構築して読み込んだりすることができます。Docker の機能やしくみの詳細については、「[What is Docker? (Docker とは)][]」を参照してください。

ほとんどのテクノロジと同様、Docker にも多くのメリットといくつかのデメリットがあります。コンテナーはホスト コンピューターのカーネルへのアクセスを共有するため、悪意のあるコードが root を取得できた場合、ホスト コンピューターだけでなく他のコンテナーにもアクセスできる可能性があります。コンテナー システムのセキュリティを強化するために、[SELinux][] や [AppArmor][] など、グループ ポリシーや[ロール ベースのセキュリティ][]を追加したり、コンテナーに使用を許可するカーネル機能をできる限り減らしたりすることが [Docker で推奨されています][]。また、インターネット上には、Docker のようなコンテナーを使用する際のセキュリティ対策について記載されたドキュメントが多数あります。

## Azure で Docker VM 拡張機能を使用する方法

Azure で Docker VM 拡張機能を使用するには、バージョン 0.8.6 以降の [azure-cli][] をインストールする必要があります (この記事の執筆時点で最新バージョンは 0.8.7 です)。azure-cli は Mac と Linux のどちらにもインストールできます。

> [WACOM.NOTE] azure-cli は Microsoft Windows にもインストールできます。ただし、Docker は Linux カーネルの依存関係を使用してビルドされているため、Windows を Docker クライアントとして使用するには、Linux のフル ディストリビューションを Hyper-V などのハイパーバイザー内に仮想マシンとしてホストする必要があります。これを実行すると、この記事や Docker ドキュメントに記載されている azure-cli と Docker のコマンドを使用することができます。Docker でも [Boot2Docker][] というセットアップ プログラムが提供されており、これを使用して同じセットアップを自動で実行できます。

Azure 上で Docker を使用するためのプロセスはシンプルです。

-   azure-cli コマンド ライン ツールとその依存関係を、Azure を制御するコンピューターにインストールする (Windows の場合は、仮想マシンとして実行される Linux ディストリビューション)。
-   azure-cli Docker コマンドを使用して、Azure に VM Docker ホストを作成する。
-   ローカルの Docker コマンドを使用して、Azure 上の Docker VM 内の Docker コンテナーを管理する。

> [WACOM.NOTE] azure-cli (コマンド ライン インターフェイス) は、現時点で、Azure 上に Docker 制御型 VM を作成して Docker コンテナーをホストする唯一の手段です。全般的なインストール マニュアルは[こちら][]から入手できます。以下のセクションでは、各種オペレーティング システムへのインストールを問題なく実行するための追加的な提案事項を説明します。

### Linux への azure-cli のインストール

Linux に azure-cli をインストールするには[ノード パッケージ マネージャー (npm)][] が必要です。また、npm には nodejs が必要となるため、使用するプラットフォームに応じて選んだ任意のパッケージ マネージャーを通じて nodejs もインストールします。npm をインストールしたら、azure-cli パッケージを取得するために、次のコマンドを入力します。

    npm install -g azure-cli

これによって、azure-cli パッケージがグローバルにインストールされます。インストールを確認するために、コマンド プロンプトで「`azure`」と入力します。しばらくすると、azure-cli の ASCII アートが表示され、使用可能な基本コマンドが一覧表示されます。インストールが正しく機能していれば、「`azure help vm`」と入力したときに、一覧表示されたコマンドに "docker" が含まれています。

> [WACOM.NOTE] Ubuntu 14.04 LTS インストールを使用する場合は、イメージのノード インストールが若干異なっているため、追加作業が必要になる場合があります。この作業に役立ちそうな提案事項が、[こちら][1]の「**How To Install Using a PPA (PPA を使用してインストールする方法)**」セクションに記載されています。この記事では、nodejs の最新バージョンを直接インストールする方法が説明されており、Ubuntu 14.04 LTS ディストリビューションにも問題なく適用できるようです。

また、ほとんどの Linux コンポーネントと同じように、ソースを複製し、コンパイルして、ローカルにインストールすることもできます。この手順を説明したドキュメントが、[][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a> にあります。

### Mac への azure-cli のインストール

Mac に azure-cli をインストールする最も簡単な方法では、やはり npm を使用し、上記と同じくコマンド `npm install -g azure-cli` を使用します。ただし、[Mac インストーラー][]を使用することもできます。Linux や Windows の場合と同様に、関連するコマンド プロンプトで「`azure`」と入力し、azure-cli がインストールされたことを確認します。

### Azure アカウントへの azure-cli の接続

azure-cli を使用する前に、プラットフォーム上で Azure アカウントの資格情報を azure-cli に関連付ける必要があります。セクション「[方法: Azure サブスクリプションに接続する][]」に、**.publishsettings** ファイルをダウンロードしてインポートする方法や azure-cli コマンド ラインを組織 ID に関連付ける方法が説明されています。認証と承認の手順については、どちらも前述のドキュメントに記載されています。

> [WACOM.NOTE] 使用する承認方法によって動作に若干の違いがあるため、必ず前述のドキュメントをよく読んで、機能の違いを理解してください。

### Docker のインストールと Azure 用 Docker VM 拡張機能の使用

ここまでで、コンピューター (またはコンピューター上の仮想マシン) に azure-cli をインストールして、Azure アカウントに接続することができました。続いては、[Docker のインストール手順][]に従って、Docker をコンピューターにローカルにインストールします。ほとんどのオペレーティング システムやディストリビューションでは、「`apt-get install docker.io`」と入力します。Docker のバージョンが 1.0 以降であることを確認してください。

これで、コンピューターに azure-cli プロンプトをインストールし、Azure アカウントに接続して、Docker をインストールできました。Azure に新しい Docker ホスト VM を作成するには、[Azure Linux VM エージェント][Azure Linux エージェント]がインストールされている Linux VM イメージが必要です。現時点では、このエージェントが事前にインストールされているイメージは、次のいずれかのみです。

-   イメージ ギャラリーで入手できる Ubuntu イメージ

-   自分で作成し、Azure Linux VM エージェントをインストールして構成したカスタム Linux イメージ。カスタム Linux VM イメージを自分で作成し、Azure VM エージェントをインストールする方法については、『[Azure Linux エージェント ユーザー ガイド][Azure Linux エージェント]』を参照してください。

azure-cli コマンド プロンプトで次のコマンドを入力して、VM ギャラリーで最新の Ubuntu イメージを見つけます。

`azure vm image list | grep Ubuntu-14_04`

一覧表示された最新のイメージのいずれかの名前をコピーします。コマンド プロンプトに、次のコマンドを入力します。

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-en-us-30GB" <username> <password>

各値の説明:

-   *<vm-cloudservice name>* は、Azure で Docker コンテナーのホスト コンピューターになる VM の名前です。

-   *<username>* は、VM の既定の root ユーザーのユーザー名です。

-   *<password>* は、Azure の複雑さの基準を満たした *username* アカウントのパスワードです。

> [WACOM.NOTE] 現時点では、パスワードは 8 文字以上で、小文字、大文字、数字、および特殊文字 ([!@\#$%^&+=][] のいずれか) を含める必要があります。

コマンドが正常に実行されると、使用した引数とオプションに応じて、以下のような応答が表示されます。

![][]

> [WACOM.NOTE] 前述のとおり、仮想マシンの作成には数分かかることがありますが、プロビジョニングが終わると、Docker デーモンが起動し、Docker コンテナー ホストに接続できるようになります。

Azure 上に作成した Docker VM をテストするために、次のコマンドを入力します。

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

*<vm-name-you-used>* は、`azure vm docker create` の呼び出しで使用した仮想マシンの名前です。この場合も、指定した引数とオプションに応じて、以下のような応答が表示されます。Azure 上で Docker ホスト VM が稼働し、コマンドの入力を待機していることが示されています。

![][2]

## Docker ホスト VM の認証に関する注意事項

コマンド `azure vm docker create` では、Docker VM の作成に加えて、必要な証明書も自動的に作成されます。Docker クライアント コンピューターは、この証明書を使用することで、HTTPS を介して Azure コンテナー ホストに接続できます。証明書は必要に応じてクライアントとホスト マシンのどちらにも格納されます。以降の実行では、既存の証明書が再利用され、新しいホストと共有されます。

既定では、証明書は `~/.docker` に配置されます。Docker はポート **4243** で実行するように構成されます。別のポートやディレクトリを使用する場合は、以下の `azure vm docker create` コマンド ライン オプションのいずれかを使用して、Docker コンテナー ホスト VM がクライアントとの接続に別のポートや証明書を使用するよう構成できます。

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

ホストの Docker デーモンは、`azure vm docker create` コマンドで生成された証明書を使用して、指定されたポートでクライアント接続をリッスンし、認証するよう構成されています。クライアント コンピューターには、Docker ホストにアクセスできるように、これらの証明書が配置されている必要があります。

> [WACOM.NOTE] 反対に、ネットワークに接続されたホストにこれらの証明書がない場合は、コンピューターに接続できる第三者に対して脆弱になります。既定の構成を変更する前に、コンピューターやアプリケーションに対するリスクについてよく理解してください。

## 次のステップ

これで、『[Docker ユーザー ガイド][]』に記載されている Docker コマンドを Azure 上の Docker ホスト VM に対して発行できるようになりました。

## Linux および Windows 向けの仮想マシン拡張機能

Azure 向けの Docker VM 拡張機能は、特殊な動作を提供する VM 拡張機能の数ある 1 つにすぎません。現在、他にも多くが開発中です。たとえば、[Linux VM エージェント拡張機能][Azure Linux エージェント]の中には、セキュリティ機能やカーネルとネットワークの機能などのイメージの変更と管理を実行できるものがあります。Windows イメージ用の VMAccess 拡張機能では、リモート デスクトップ アクセス設定のリセットや変更、管理者パスワードのリセットを実行できます。

詳しい一覧は、「[拡張機能の管理][]」を参照してください。

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [Linux コンテナー]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux エージェント]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/
  [MS Open Tech ブログのお知らせ]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Docker と Linux コンテナー]: #Docker-and-Linux-Containers
  [Azure で Docker VM 拡張機能を使用する方法]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Linux および Windows 向けの仮想マシン拡張機能]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Azure 向けのコンテナーおよびコンテナー管理リソース]: #Container-and-Container-Management-Resources-for-Azure
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [ハイパーバイザー]: http://en.wikipedia.org/wiki/Hypervisor
  [What is Docker? (Docker とは)]: https://www.docker.com/whatisdocker/
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [ロール ベースのセキュリティ]: http://en.wikipedia.org/wiki/Role-based_access_control
  [Docker で推奨されています]: https://docs.docker.com/articles/security/
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [こちら]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#install
  [ノード パッケージ マネージャー (npm)]: http://en.wikipedia.org/wiki/Npm_%28software%29
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [Mac インストーラー]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [方法: Azure サブスクリプションに接続する]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#configure
  [Docker のインストール手順]: https://docs.docker.com/installation/#installation
  [!@\#$%^&+=]: mailto:!@#$%^&+=
  []: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Docker ユーザー ガイド]: https://docs.docker.com/userguide/
  [拡張機能の管理]: http://msdn.microsoft.com/en-us/library/azure/dn606311.aspx

<properties
   pageTitle="Azure における 3 ノード Deis クラスターの展開"
   description="Azure 上で3 ノード Devis クラスターを展開"
   services="virtual-machines"
   documentationCenter=""
   authors="HaishiBai"
   manager="larar"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# 3 ノード Deis クラスターの展開

この記事では Azure での[Deis](http://deis.io/) クラスターのプロビジョニングをについて説明します。必要な証明書の作成から新しくプロビジョニングされたクラスタ上にサンプルの **Go** アプリケーション を展開・スケーリングする手順まで、すべての手順を網羅しています。

次の図は、展開済みシステムのアーキテクチャを示します。システム管理者は**deis** と **deisctl**などの Deis ツールを使ってクラスタを管理します。接続は、クラスター上のメンバーノードの 1 つに、接続を転送をする。Azure ロード バランサーを介して確立されます。同様に Load Balancer を使用してアプリケーションをクライアントがアクセスに展開されます。この場合は、Load Balancer は Deis ルーターメッシュへトラフィックを転送します。さらに、クラスターでホストされている、対応する Docker コンテナーへのトラフィックをルートします。

  ![配展開済みの Desis クラスターのアーキテクチャ図](media/virtual-machines-deis-cluster/architecture-overview.png)

次の手順を実行するため、以下の項目が必要となります。

 * 有効な Azure サブスクリプション有効なサブスクリプションがない場合は、[azure.com](https://azure.microsoft.com)から無償の試用版を取得できます
 * Azure リソース グループを使用する職場または学校の ID 。個人のアカウントをお持ちの場合で、Microsoft id を使用してログインする場合 [個人の アカウントから作業の ID を作成する](resource-group-create-work-id-from-personal.md)ことができます。
 * または、いずれかの--クライアント オペレーティング システムに応じて、 [Azure PowerShell](powershell-install-configure.md) または [、Mac、Linux、および Windows 用の Azure CLI](xplat-cli-install.md)が必要です。
 * [OpenSSL](https://www.openssl.org/)。OpenSSL を使用して、必要な証明書が生成されます。
 * [Git Bash](https://git-scm.com/)などの Git クライアント。
 * サンプル アプリケーションをテストするには、DNS サーバーが必要です。任意の DNS サーバーまたはワイルドカードの A レコードをサポートするサービスを使用することもできます。
 * Deis クライアント ツールを実行するコンピューターに。ローカル コンピューターまたは仮想マシンのいずれかを使用することができます。ほぼすべての Linux ディストリビューションでこれらのツールを実行することができますが、次の手順では、Ubuntu を使用します。

## クラスタのプロビジョニング

このセクションでは、 [Azure リソース マネージャー](../resource-group-overview.md) テンプレートをオープン ソース リポジトリ [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)から使用します。最初に、テンプレートをコピーします。次に、認証の新しい SSH キー ペアを作成します。次に、クラスターの新しい識別子を構成します。最後に Shell スクリプトまたは PowerShell スクリプトのいずれかを、クラスターのプロビジョニングに使用します。

1. リポジトリを複製: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)

        git clone https://github.com/Azure/azure-quickstart-templates

2. テンプレートのフォルダーを参照してください :

        cd azure-quickstart-templates\deis-cluster-coreos

3. ssh-keygen を使用して、新しい SSH キー ペアを作成します:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. 上記の秘密キーを使用して、証明書を生成します:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. [https://discovery.etcd.io/new](https://discovery.etcd.io/new) に移動して新しいクラスター トークンを生成します。

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />各 CoreOS クラスターは、この無料サービスからの一意のトークンが必要です。詳細は[CoreOS ドキュメンテーション](https://coreos.com/docs/cluster-management/setup/cluster-discovery/)をご覧ください。

6. **cloud-config.yaml**ファイルを変更して、既存のfile to replace the existing **discovery**トークンを新しいトークンと置き換えます：

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. **azuredeploy parameters.json** ファイルの変更：手順 4. で作成した証明書をテキスト エディターで開きます。`----BEGIN CERTIFICATE-----` と `-----END CERTIFICATE-----` の間のテキストを **sshKeyData**パラメータにコピーします(すべての改行文字を削除する必要があります)。

8. **newStorageAccountName** パラメータを修正します。これは、VM OS ディスクのストレージ アカウントです。このアカウントの名前はグローバルに一意です。

9. **publicDomainName** パラメータを修正します。これが Load Balancer のパブリック IP に関連付けられている DNS 名の一部になります。最後の FQDN は_ [このパラメーターの値]_._[region]_ cloudapp.azure.co の形式になります。たとえば、deishbai32 として名前を指定して、リソース グループが米国西部地域に展開されている場合、Load Balancer の最終的な FQDN は deishbai32.westus.cloudapp.azure.com となります。

10. パラメーター ファイルを保存します。Azure PowerShell を使用してプロビジョニングするには、次の手順を実行します。

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  Azure CLI の場合

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. リソース グループがプロビジョニングされると、Azure ポータルで、グループ内のすべてのリソースを確認できます。次のスクリーン ショットが示すように、リソース グループには、同じ可用性セットに参加している次の 3 つの VM を伴う仮想ネットワークが含まれます。このグループには関連付けられているパブリック IP を持つ Load Balancer も含まれます。

  ![Azure ポータルでプロビジョニングされているリソース グループ](media/virtual-machines-deis-cluster/resource-group.png)

## クライアントをインストールします。

Deis クラスタをコントロールする **deisctl**　が必要となります。Deisctl はすべてのクラスター ノードで自動的にインストールされますが、管理用の個別のマシンで deisctl を使用することを推奨します。さらに、プライベート IP アドレスのみを持つすべてのノードを構成するため Load Balancer は、ノード コンピューターへの接続に、パブリック ip 経由のトンネリング SSH を使用する必要があります。物理マシンまたは仮想マシン上に個別の Ubuntu で deisctl をセットアップする手順を次に示します。

1. deisctl:mkdir deis をインストールします

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. ssh エージェントに秘密キーを追加します。

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. deisctl を構成します

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

テンプレートは 2223 をインスタンス 1 に、 2224 をインスタンス 2 に、2225 をインスタンス 3 にマップするインバウンド NAT 規則を定義します。これは、deisctl ツールを使用するための冗長性を提供します。これらのルールは Azure ポータルで確認することができます。

![Load Balancer 上の NAT ルール](media/virtual-machines-deis-cluster/nat-rules.png)

> [AZURE.NOTE]現在、テンプレートは 3 ノード クラスタのみをサポートしています。これは、ループ構文をサポートしていない Azure Resource Manager テンプレート NAT ルール定義の制限によるものです。

## Deis プラットフォームのインストールと開始

これで、Deis プラットフォームをインストールして開始する Deisctl を使用できるようになりました。

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE]プラットフォームの開始には少し時間がかかります (10 分ほど)。特に、ビルダー サービスの開始には時間が長くかかることがあります。また時々、試行が成功するかかる場合があります。 操作応答を停止する場合は、入力してみて `ctrl+c` をコマンドの実行を中断し、再試行してください。

すべてのサービスが実行されているかどうかを確認するには `deisctl list` を使用できます。

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

ご利用ありがとうございます。 これで Azure で Deis クラスターを実行することができました。 次に、サンプルの Go アプリケーションを展開してみましょう。クラスタはどう動作するでしょうか。

## Hello World アプリケーションを展開、スケールします。

次の手順は、"Hello World" の Go アプリケーションをクラスタに展開する方法を示します。この手順は[Deis ドキュメンテーション](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles)に基づいています。

1. ルーティングメッシュを適切に機能させるには、Load Balancer のパブリック IP アドレスをポイントするドメインのワイルドカード A レコードを作成する必要があります。次のスクリーン ショットは、GoDaddy のサンプル ドメイン登録用の A レコードです。

    ![Godaddy A レコード](media/virtual-machines-deis-cluster/go-daddy.png)
<p />
2. deis のインストール:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. 新しい SSH キーを作成してその公開キーを GitHub (既存のキーはもちろんも再利用できます)に追加します。ssh-keygen を使用して、新しい SSH キー ペアを作成します:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. GitHub に id\_rsa.pub または任意の公開キーを追加します。これは、SSH キー構成画面で SSH キー追加 ボタンを使用して実行できます。

  ![Github キー](media/virtual-machines-deis-cluster/github-key.png) <p /> 5.新しいユーザーの登録

        deis register http://deis.[your domain]
<p />
6. SSH キーの追加:

        deis keys:add [path to your SSH public key]
  <p />
7. アプリケーションの作成

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8. git プッシュが Docker イメージを構築し展開するようトリガーします。これには数分かかります。私の経験では、場合によっては手順 10 (プライベート リポジトリにイメージをプッシュ) でハングアップする場合があります。この場合、プロセスを停止して、`deis apps:destroy –a <application name>` を使用してアプリケーションを削除し、もう一度やり直してください。 `deis apps:list` を使用してアプリケーションの名前を確認できます。すべての動作が確認されたら、コマンドの出力の最後に、次のようなものが表示されます。

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. アプリケーションが動作しているかどうかを確認します:

        curl -S http://[your application name].[your domain]
  次のような結果が表示されます。

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. 3 つのインスタンスに、アプリケーションをスケール:

        deis scale cmd=3
<p />
11. 必要に応じて、deis 情報を使用して、アプリケーションの詳細を確認します。次の出力は、私のアプリケーション展開です:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## 次のステップ

この記事ではAzure リソース マネージャーテンプレートを使用して Azure 上に新しい Deis プロビジョニングするすべての手順を説明 しました。このテンプレートは、接続ツールの冗長性と展開アプリケーションの付加バランスをサポートします。このテンプレートにより、メンバー ノードでのパブリック IP の使用を回避します。これにより貴重なパブリック IP のリソースを保存し、アプリケーションのホストに安全な環境を提供します。詳細については、次の記事を参照してください。

[Azure リソース マネージャーの概要][resource-group-overview] [Azure CLI の使用方法][azure-command-line-tools] [Asure リソース マネージャーでの Azure PowerShell の使用方法][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=August15_HO6-->
<properties
   pageTitle="Mac OS X での開発環境のセットアップ | Microsoft Azure"
   description="ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。このセットアップを完了すると、Mac OS X でアプリケーションを構築する準備が整います。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# Mac OS X で開発環境をセットアップする

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Linux クラスターで実行される Service Fabric アプリケーションを Mac OS X を使用して構築できます。この記事では、開発用に Mac をセットアップする方法について説明します。

## 前提条件

Service Fabric は、OS X ではネイティブに実行されません。Microsoft では、ローカルの Service Fabric クラスターを実行できるように、Vagrant と VirtualBox を使用して事前に構成済みの Ubuntu 仮想マシンを用意しています。作業を開始する前に、以下を行う必要があります。

- [Vagrant (v1.8.4 以降)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## ローカル VM を作成する

5 ノード構成の Service Fabric クラスターを保持するローカル VM を作成するには、次の手順に従います。

1. Vagrantfile リポジトリをクローンします。

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. リポジトリのローカル クローンに移動します。

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (省略可能) 既定の VM 設定を変更します。

    既定では、ローカル VM は次のように構成されています。

    - メモリの割り当て 3 GB
    - IP 192.168.50.50 で構成されたプライベート ホスト ネットワーク。Mac ホストからのトラフィックに対してパススルーが有効化されています。

    この設定のどちらかを変更することも、Vagrantfile で VM に他の構成を追加することもできます。構成オプションの詳細な一覧については、[Vagrant のドキュメント](http://www.vagrantup.com/docs)をご覧ください。

4. VM の作成

    ```bash
    vagrant up
    ```

    この手順を実行すると、事前構成済みの VM イメージがダウンロードされてローカルで起動され、ローカル Service Fabric クラスターがセットアップされます。この処理には数分かかります。セットアップが正常に完了すると、クラスターが開始されていることを示すメッセージが出力に表示されます。

    ![Cluster setup starting following VM provisioning][cluster-setup-script]

5. クラスターが正しくセットアップされていることをテストするために、Service Fabric Explorer (http://192.168.50.50:19080/Explorer) に移動します (プライベート ネットワーク IP アドレスが既定値のままであると仮定しています)。

    ![Service Fabric Explorer viewed from the host Mac][sfx-mac]


## Eclipse Neon 用の Service Fabric プラグインをインストールする (省略可能)

Service Fabric には、Java サービスの構築とデプロイのプロセスを簡略化できる Eclipse Neon IDE 用プラグインが用意されています。

1. Eclipse で、Buildship バージョン 1.0.17 以降がインストールされていることを確認します。**[Help (ヘルプ)]、[Installation Details (インストールの詳細)]** の順に選択して、インストールされたコンポーネントのバージョンを確認できます。Buildship は、[こちら][buildship-update]の手順に従って更新できます。

2. **[Help (ヘルプ)]、[Install New Software (新しいソフトウェアのインストール)]** を選択して、Service Fabric プラグインをインストールします。

3. [Work with (作業対象)] テキストボックスに、次のように入力します。http://dl.windowsazure.com/eclipse/servicefabric

4. [追加] をクリックします。

    ![Eclipse Neon plugin for Service Fabric][sf-eclipse-plugin-install]

5. Service Fabric プラグインを選択し、[Next (次へ)] をクリックします。

6. 指示に従ってインストールを実行し、使用許諾契約書に同意します。

## 次のステップ

- [最初の Linux 向け Service Fabric アプリケーションを作成する](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Azure Portal で Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)
- [Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)
- [Service Fabric アプリケーション モデルを理解する](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!---HONumber=AcomDC_0928_2016-->
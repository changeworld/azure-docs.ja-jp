---
title: "Mac OS X での開発環境のセットアップ | Microsoft Docs"
description: "ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを完了すると、Mac OS X でアプリケーションを構築する準備が整います。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d5a04e34a3f46097b3ad1b04ac6b2b845fc946af
ms.openlocfilehash: beb869fd5c1fcc19101b16149244187c37a6d6a3


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Mac OS X で開発環境をセットアップする
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux クラスターで実行される Service Fabric アプリケーションを Mac OS X を使用して構築できます。この記事では、開発用に Mac をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件
Service Fabric は、OS X ではネイティブに実行されません。Microsoft では、ローカルの Service Fabric クラスターを実行できるように、Vagrant と VirtualBox を使用して事前に構成済みの Ubuntu 仮想マシンを用意しています。 作業を開始する前に、以下を行う必要があります。

* [Vagrant (v1.8.4 以降)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>ローカル VM を作成する
5 ノード構成の Service Fabric クラスターを保持するローカル VM を作成するには、次の手順を実行します。

1. **Vagrantfile** リポジトリを複製します。
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. リポジトリのローカル クローンに移動します。

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (省略可能) 既定の VM 設定を変更します。

    既定では、ローカル VM は次のように構成されています。

   * メモリの割り当て 3 GB
   * IP 192.168.50.50 で構成されたプライベート ホスト ネットワーク。Mac ホストからのトラフィックに対してパススルーが有効化されています。

     この設定のどちらかを変更することも、Vagrantfile で VM に他の構成を追加することもできます。 構成オプションの詳細な一覧については、 [Vagrant のドキュメント](http://www.vagrantup.com/docs) をご覧ください。
4. VM の作成

    ```bash
    vagrant up
    ```
  
   この手順を実行すると、事前構成済みの VM イメージがダウンロードされてローカルで起動され、ローカル Service Fabric クラスターがセットアップされます。 この処理には数分かかります。 セットアップが正常に完了すると、クラスターが開始されていることを示すメッセージが出力に表示されます。
   
    ![Cluster setup starting following VM provisioning][cluster-setup-script]
    
5. クラスターが正しくセットアップされていることをテストするために、Service Fabric Explorer (http://192.168.50.50:19080/Explorer) に移動します (プライベート ネットワーク IP アドレスが既定値のままであると仮定しています)。

    ![Service Fabric Explorer viewed from the host Mac][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Eclipse Neon 用の Service Fabric プラグインをインストールする (省略可能)
Service Fabric には、Java サービスの構築とデプロイのプロセスを簡略化できる Eclipse Neon IDE 用プラグインが用意されています。

1. Eclipse で、Buildship バージョン 1.0.17 以降がインストールされていることを確認します。 **[Help (ヘルプ)]、[Installation Details (インストールの詳細)]** の順に選択して、インストールされたコンポーネントのバージョンを確認できます。 Buildship は、[こちら][buildship-update]の手順に従って更新できます。
2. **[Help (ヘルプ)]、[Install New Software (新しいソフトウェアのインストール)]** の順に選択して、Service Fabric プラグインをインストールします。
3. [Work with (作業対象)] ボックスに、「http://dl.windowsazure.com/eclipse/servicefabric」と入力します。
4. [追加] をクリックします。

    ![Eclipse Neon plugin for Service Fabric][sf-eclipse-plugin-install]
5. Service Fabric プラグインを選択し、[Next (次へ)] をクリックします。
6. 指示に従ってインストールを実行し、使用許諾契約書に同意します。

## <a name="next-steps"></a>次のステップ
<!-- Links -->

* [最初の Linux 向け Service Fabric アプリケーションを作成する](service-fabric-create-your-first-linux-application-with-java.md)
* [Azure Portal で Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)
* [Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)
* [Service Fabric アプリケーション モデルを理解する](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Jan17_HO1-->



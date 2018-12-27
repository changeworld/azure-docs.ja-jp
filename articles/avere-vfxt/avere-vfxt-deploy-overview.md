---
title: デプロイの概要 - Avere vFXT for Azure
description: Avere vFXT for Azure のデプロイの概要
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669819"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - デプロイの概要

この記事では、Avere vFXT for Azure クラスターを起動して実行するために必要な手順の概要を示します。

初めて Avere vFXT システムをデプロイする場合、他のほとんどの Azure ツールをデプロイするときよりも多くの手順に関与していることにお気付きかもしれません。 開始から終了までのプロセスを明確に理解すると、必要な作業量の範囲を絞り込むことができます。 システムを起動して実行したら、クラウドベースのコンピューティング タスクを迅速に処理するその能力は、労力に値するものになります。

## <a name="deployment-steps"></a>デプロイメントの手順

[システムを計画](avere-vfxt-deploy-plan.md)した後は、自分の Avere vFXT クラスターの作成を開始できます。 

クラスター コントローラー VM の作成から開始されますが、これは vFXT クラスターの作成に使用されます。

vFXT クラスターを起動して実行したら、クライアントを vFXT クラスターに接続する方法、および必要に応じて、自分のデータを新しい BLOB ストレージ コンテナーに移動する方法を理解する必要があります。  

すべての手順の概要は次のとおりです。

1. 構成の前提条件 

   VM を作成する前に、Avere vFXT プロジェクトに対して新しいサブスクリプションを作成し、サブスクリプションの所有権を構成し、クォータを確認して必要に応じて増やすように要求し、Avere vFXT ソフトウェアの使用に対する使用条件に同意する必要があります。 詳細な手順については、「[Avere vFXT の作成準備](avere-vfxt-prereqs.md)」を参照してください。

1. クラスター コントローラーを作成する

   *クラスター コントローラー*は、Avere vFXT クラスターと同じ仮想ネットワークにあるシンプルな VM です。 コントローラーによって vFXT ノードが作成され、クラスターが形成されます。また、その有効期間中にクラスターを管理するために、コマンド ライン インターフェイスも提供されます。

   コントローラーをパブリック IP アドレスを使って構成する場合、VNet 外からクラスターに接続するためのジャンプ ホストとして使用することもできます。

   vFXT クラスターを作成してそのノードを管理するために必要なソフトウェアはすべて、クラスター コントローラーにプレインストールされています。

   詳細については、「[クラスター コントローラー VM を作成する](avere-vfxt-deploy.md#create-the-cluster-controller-vm)」を参照してください。

1. クラスター ノード用にランタイム ロールを作成する 

   Azure では、[ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) を使用し、クラスター ノード VM を承認して特定のタスクを実行します。 たとえば、クラスター ノードは、IP アドレスをその他のクラスター ノードに割り当てまたは再割り当てすることができる必要があります。 クラスターを作成する前に、適切なアクセス権限を与えるロールを定義する必要があります。

   クラスター コントローラーにプレインストールされているソフトウェアには、カスタマイズできるように、プロトタイプのロールが含まれています。 手順については、「[クラスター ノードのアクセス ロールを作成する](avere-vfxt-deploy.md#create-the-cluster-node-access-role)」を参照してください。

1. Avere vFXT クラスターを作成する 

   コントローラー上で、適切なクラスターの作成スクリプトを編集し、それを実行してクラスターを作成します。 「[デプロイ スクリプトを編集する](avere-vfxt-deploy.md#edit-the-deployment-script)」には、詳細な手順が含まれます。 

1. クラスターを構成する 

   Avere vFXT の構成インターフェイス (Avere Control Panel) に接続して、クラスターの設定をカスタマイズします。 サポートの監視を許可し、オンプレミスのデータ センターを使用している場合は、ご利用のストレージ システムを追加します。

   * [vFXT クラスターへのアクセス](avere-vfxt-cluster-gui.md)
   * [サポートの有効化](avere-vfxt-enable-support.md)
   * [ストレージの構成](avere-vfxt-add-storage.md) (必要に応じて)

1. クライアントをマウントする

   負荷分散について、およびクライアント マシンでクラスターをマウントする方法については、「[Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md)」 (Avere vFXT クラスターをマウントする) のガイドラインに従います。

1. データの追加 (必要に応じて)

   Avere vFXT はスケーラブルな複数のクライアントのキャッシュであるため、新しいバックエンド ストレージ コンテナーにデータを移動する最適な方法は、複数のクライアント (マルチスレッドのファイル コピーの戦略) を使用することです。 詳細については、[vFXT クラスターへのデータの移動](avere-vfxt-data-ingest.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

「[Avere vFXT の作成準備](avere-vfxt-prereqs.md)」に進み、Avere vFXT for Azure をデプロイするための事前タスクを完了します。 
---
title: Avere vFXT の前提条件 - Azure
description: Avere vFXT for Azure の前提条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669529"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Avere vFXT の作成を準備する

この記事では、Avere vFXT クラスターを作成するための前提条件タスクについて説明します。

## <a name="create-a-new-subscription"></a>新しいサブスクリプションを作成する

最初に、新しい Azure サブスクリプションを作成します。 Avere vFXT プロジェクトごとに個別のサブスクリプションを使用することで、プロジェクトのすべてのリソースと経費を簡単に追跡し、プロビジョニング中に他のプロジェクトのリソースが調整される可能性を防ぎ、簡単にクリーンアップできるようにします。  

Azure portal で新しい Azure サブスクリプションを作成するには:

* [[サブスクリプション] ブレード](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動します
* 上部にある **[+ 追加]** ボタンをクリックします
* 求められた場合はサインインします
* オファーを選択し、手順に従って新しいサブスクリプションを作成します

## <a name="configure-subscription-owner-permissions"></a>サブスクリプション所有者のアクセス許可を構成する

サブスクリプションの所有者アクセス許可を持つユーザーが、vFXT クラスターを作成する必要があります。 たとえば次のような操作に、サブスクリプション所有者のアクセス許可が必要です。

* Avere vFXT ソフトウェアの条件に同意する
* クラスター ノードのアクセス ロールを作成する
* リソース グループと仮想ネットワークの管理を、クラスター コントローラー ノードに許可する 
* クラスター ノードの作成と変更を、クラスター コントローラーに許可する 

vFXT を作成するユーザーに所有者アクセス権を付与したくない場合は、2 つの回避策があります。

* 次の条件が満たされる場合、リソース グループ所有者がクラスターを作成できます。

  * サブスクリプション所有者が、[Avere vFXT ソフトウェアの条件に同意](#accept-software-terms-in-advance)し、[クラスター ノードのアクセス ロールを作成する](avere-vfxt-deploy.md#create-the-cluster-node-access-role)必要があります。
  * 次のような Avere vFXT のすべてのリソースを、リソース グループ内に展開する必要があります。
    * クラスター コントローラー
    * クラスター ノード
    * BLOB ストレージ
    * ネットワーク要素
 
* 所有者特権を持たないユーザーの場合、事前にアクセス ロールを追加作成してユーザーに割り当てることで、vFXT クラスターを作成できます。 ただし、このロールでは、ユーザーに重要なアクセス許可が与えられます。 所有者以外のユーザーにクラスターの作成を承認する方法については、[こちらの記事](avere-vfxt-non-owner.md)をご覧ください。

## <a name="quota-for-the-vfxt-cluster"></a>vFXT クラスターのクォータ

次の Azure コンポーネントに対する十分なクォータが必要です。 必要な場合は、[クォータの増量を要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)します。

> [!NOTE]
> ここで示す仮想マシンと SSD コンポーネントは、vFXT クラスター自体のためのものです。 自分の計算ファーム用に使用する VM と SSD には、追加のクォータが必要です。  ワークフローを実行するリージョンでクォータが有効になっていることを確認します。

|Azure のコンポーネント|Quota|
|----------|-----------|
|仮想マシン|3 つ以上の D16s_v3 または E32s_v3|
|Premium SSD ストレージ|200 GB の OS スペース、およびノードあたり 1 から 4 TB のキャッシュ |
|ストレージ アカウント (オプション) |v2|
|データ バックエンド ストレージ (オプション) |1 つの新しい LRS BLOB コンテナー |

## <a name="accept-software-terms-in-advance"></a>事前にソフトウェアの条件に同意する

> [!NOTE] 
> サブスクリプションの所有者が Avere vFXT クラスターを作成する場合は、このステップは必要ありません。

クラスターを作成する前に、Avere vFXT ソフトウェアのサービスの条件に同意する必要があります。 サブスクリプションの所有者でない場合は、前もってサブスクリプションの所有者に条件に同意してもらいます。 このステップは、サブスクリプションごとに 1 回だけ実行する必要があります。

事前にソフトウェアの条件に同意するには: 

1. Azure portal で Cloud Shell を開くか、または <https://shell.azure.com> にアクセスします。 サブスクリプション ID でサインインします。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 次のコマンドを実行して、サービスの条件に同意し、Avere vFXT for Azure ソフトウェア イメージへのプログラム アクセスを有効にします。 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>次のステップ: vFXT クラスターを作成する

以上の前提条件を完了すると、クラスター自体の作成に進むことができます。 手順については、「[Deploy the vFXT cluster](avere-vfxt-deploy.md)」(vFXT クラスターをデプロイする) をご覧ください。
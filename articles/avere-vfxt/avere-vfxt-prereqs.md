---
title: Avere vFXT の前提条件 - Azure
description: Avere vFXT for Azure の前提条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 9c3301ba16bfaeb7014658a380e287a36a505be8
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299208"
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

vFXT を作成するユーザーに所有者アクセス権を付与したくない場合は、2 つの回避策があります。

* 次の条件が満たされる場合、リソース グループ所有者がクラスターを作成できます。

  * サブスクリプション所有者が、[Avere vFXT ソフトウェアの条件に同意](#accept-software-terms)し、[クラスター ノードのアクセス ロールを作成する](#create-the-cluster-node-access-role)必要があります。 
  * 次のような Avere vFXT のすべてのリソースを、リソース グループ内に展開する必要があります。
    * クラスター コントローラー
    * クラスター ノード
    * BLOB ストレージ
    * ネットワーク要素
 
* 所有者特権のないユーザーは、前もってロールベースのアクセス制御 (RBAC) を使用してそのユーザーに特権を割り当てることで、vFXT クラスターを作成できます。 この方法では、ユーザーに重要なアクセス許可が与えられます。 所有者以外のユーザーにクラスターの作成を承認するためのアクセス ロールを作成する方法については、[こちらの記事](avere-vfxt-non-owner.md)をご覧ください。

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

## <a name="accept-software-terms"></a>ソフトウェアの条件に同意する

> [!NOTE] 
> サブスクリプションの所有者が Avere vFXT クラスターを作成する場合は、このステップは必要ありません。

クラスターの作成時に、Avere vFXT ソフトウェアのサービスの条件に同意する必要があります。 サブスクリプションの所有者でない場合は、前もってサブスクリプションの所有者に条件に同意してもらいます。 このステップは、サブスクリプションごとに 1 回だけ実行する必要があります。

事前にソフトウェアの条件に同意するには: 

1. Azure portal で Cloud Shell を開くか、または <https://shell.azure.com> にアクセスします。 サブスクリプション ID でサインインします。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 次のコマンドを実行して、サービスの条件に同意し、Avere vFXT for Azure ソフトウェア イメージへのプログラム アクセスを有効にします。 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-access-roles"></a>アクセス ロールを作成する 

[ロールベースのアクセス制御](../role-based-access-control/index.yml) (RBAC) により、vFXT クラスター コントローラーとクラスター ノードに、必要なタスクを実行する承認が与えられます。

* クラスターを作成するために、クラスター コントローラーには VM を作成して変更するためのアクセス許可が必要です。 

* 個々の vFXT ノードでは、通常のクラスター操作の一環として、Azure リソース プロパティの読み取り、ストレージの管理、他のノードのネットワーク インターフェイス設定の制御などを行う必要があります。

Avere vFXT クラスターを作成する前に、クラスター ノードで使用するカスタム ロールを定義する必要があります。 

クラスター コントローラーについては、テンプレートの既定のロールをそのまま使用できます。 既定では、クラスター コントローラーにはリソース グループの所有者特権が与えられます。 コントローラー用にカスタム ロールを作成する場合は、「[コントローラーのアクセス ロールのカスタマイズ](avere-vfxt-controller-role.md)」を参照してください。

> [!NOTE] 
> ロールを作成できるのは、サブスクリプションの所有者と、所有者ロールまたはユーザー アクセス管理者ロールを持つユーザーのみです。 ロールは前もって作成できます。  

### <a name="create-the-cluster-node-access-role"></a>クラスター ノードのアクセス ロールを作成する

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Azure クラスターの Avere vFXT を作成する前に、クラスター ノードのロールを作成する必要があります。

> [!TIP] 
> Microsoft の内部ユーザーは、作成する代わりに "Avere Cluster Runtime Operator" という名前の既存のロールを使用する必要があります。 

1. このファイルをコピーします。 AssignableScopes の行にサブスクリプション ID を追加します。

   (このファイルの現在のバージョンは、github.com/Azure/Avere リポジトリに [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt) として格納されています。)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. このファイルを ``avere-operator.json`` として、または同様の覚えやすいファイル名で保存します。 


1. Azure Cloud Shell を開き、お使いの ([このドキュメントで前に](#accept-software-terms)説明した) サブスクリプション ID を使用してサインインします。 以下のコマンドを使用して、ロールを作成します。

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

ロール名は、クラスターの作成時に使用されます。 この例では、名前は ``avere-operator`` です。

## <a name="next-step-create-the-vfxt-cluster"></a>次のステップ: vFXT クラスターを作成する

以上の前提条件を完了すると、クラスター自体の作成に進むことができます。 手順については、「[Deploy the vFXT cluster](avere-vfxt-deploy.md)」(vFXT クラスターをデプロイする) をご覧ください。
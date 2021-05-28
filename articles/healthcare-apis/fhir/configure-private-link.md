---
title: Azure API for FHIR 用のプライベート リンク
description: この記事では、Azure API for FHIR サービス用のプライベート エンドポイントを設定する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/27/2021
ms.author: zxue
ms.openlocfilehash: 6b025c35c5faa64a2333710693c992a410cafecc
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652725"
---
# <a name="configure-private-link"></a>プライベート リンクを構成する

プライベートリンクを使用すると、プライベートエンドポイント経由で FHIR の Azure API にアクセスできます。これは、仮想ネットワークのプライベート IP アドレスを使用してプライベートかつ安全に接続するネットワークインターフェイスです。 プライベートリンクを使用すると、パブリックドメインネームシステム (DNS) を経由せずに、最初のパーティサービスとして VNet からサービスに安全にアクセスできます。 この記事では、Azure API for FHIR のプライベートエンドポイントを作成、テスト、および管理する方法について説明します。

>[!Note]
>Private Link を有効にすると、Private Link または Azure API for FHIR も、あるリソース グループまたはサブスクリプションから別のリソース グループまたはサブスクリプションに移動できなくなります。 移動を行うには、まずプライベートリンクを削除してから、Azure API for FHIR を移動します。 移動が完了したら、新しいプライベートリンクを作成します。 Private Link を削除する前に、潜在的なセキュリティの問題を評価します。
>
>FHIR の Azure API で監査ログとメトリックのエクスポートが有効になっている場合は、ポータルから **診断設定** を使用してエクスポート設定を更新します。

## <a name="prerequisites"></a>前提条件

プライベートエンドポイントを作成する前に、いくつかの Azure リソースを最初に作成する必要があります。

- リソース グループ – 仮想ネットワークとプライベート エンドポイントを含める Azure リソース グループ。
- Azure API for FHIR – プライベート エンドポイントの背後に配置する FHIR リソース。
- 仮想ネットワーク – クライアント サービスとプライベート エンドポイントが接続される VNet。

詳細については、 [プライベートリンクのドキュメント](../../private-link/index.yml)を参照してください。

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

プライベートエンドポイントを作成するために、FHIR リソースに対するロールベースのアクセス制御 (RBAC) アクセス許可を持つ開発者は、Azure portal、 [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)、または [Azure CLI](../../private-link/create-private-endpoint-cli.md)を使用できます。 この記事では、Azure portal を使用する手順について説明します。 プライベート DNS ゾーンの作成と構成を自動化するため、Azure portal を使用することをお勧めします。 詳細については、「 [プライベートリンククイックスタートガイド](../../private-link/create-private-endpoint-portal.md)」を参照してください。

プライベート エンドポイントを作成するには、2 つの方法があります。 自動承認フローを使用すると、FHIR リソースに対する RBAC アクセス許可を持つユーザーは、承認を受ける必要なく、プライベート エンドポイントを作成できます。 手動承認フローを使用すると、FHIR リソースに対するアクセス許可を持たないユーザーは、プライベート エンドポイントを FHIR リソースの所有者に承認してもらうよう要求できます。

> [!NOTE]
> Azure API for FHIR に対して承認されたプライベートエンドポイントが作成されると、そのエンドポイントへのパブリックトラフィックが自動的に無効になります。 

### <a name="auto-approval"></a>自動承認

新しいプライベートエンドポイントのリージョンが仮想ネットワークのリージョンと同じであることを確認します。 FHIR リソースのリージョンは異なっていてもかまいません。

![Azure portal の [基本] タブ](media/private-link/private-link-portal2.png)

リソースの種類については、 **HealthcareApis/services** を検索して選択します。 リソースに対して、[FHIR] リソースを選択します。 [ターゲットサブリソース] で [ **Fhir**] を選択します。

![Azure portal の [リソース] タブ](media/private-link/private-link-portal1.png)

既存のプライベート DNS ゾーンが設定されていない場合は、[ **(新規) privatelink azurehealthcareapis** を選択します。 プライベート DNS ゾーンが既に構成されている場合は、一覧からそれを選択できます。 **Privatelink.azurehealthcareapis.com** の形式である必要があります。

![Azure Portal の [構成] タブ](media/private-link/private-link-portal3.png)

デプロイが完了したら、[プライベート エンドポイント接続]タブに戻り、接続状態として **[承認済** み] と表示されます。

### <a name="manual-approval"></a>手動承認

手動承認の場合は、[リソース] の下の 2 つ目のオプションである [リソース ID またはエイリアスを使って Azure リソースに接続します] を選択します。 [ターゲット サブリソース] で、自動承認と同様に「fhir」と入力します。

![手動承認](media/private-link/private-link-manual.png)

デプロイが完了したら、[プライベート エンドポイント接続] タブに戻ります。ここでは、接続の承認、拒否、または削除を行うことができます。

![オプション](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>プライベート エンドポイントをテストする

パブリック ネットワーク アクセスを無効にした後に FHIR サーバーがパブリック トラフィックを受信していない状態を確認するには、コンピューターからサーバーの /metadata エンドポイントを選択します。 "403 Forbidden" (403 許可されていません) という応答を受け取るはずです。 


> [!NOTE]
> パブリック トラフィックがブロックされる前に、パブリック ネットワーク アクセス フラグを更新してから最大 5 分かかる場合があります。

プライベート エンドポイントがサーバーにトラフィックを送信できるよう、次の点を確認します。

1. プライベート エンドポイントが構成されている仮想ネットワークとサブネットに接続されている仮想マシン (VM) を作成します。 VM からのトラフィックがプライベート ネットワークのみを使用している場合は、ネットワーク セキュリティ グループ (NSG) 規則を使用して送信インターネット トラフィックを無効にします。
2. RDP で VM に接続します。
3. VM から FHIR サーバーの /metadata エンドポイントにアクセスします。 応答として機能ステートメントを受け取る必要があります。

## <a name="manage-private-endpoint"></a>マネージド プライベート エンドポイント

### <a name="view"></a>表示

プライベート エンドポイントと関連付けられているネットワーク インターフェイス コントローラー (NIC) は、作成されたリソース Azure portalグループからネットワーク に表示されます。

![リソースでの表示](media/private-link/private-link-view.png)

### <a name="delete"></a>削除

プライベート エンドポイントは、[概要] ブレードから Azure portal から、または[ネットワーク プライベート エンドポイント接続] タブの [削除] オプションを選択することでのみ **削除** できます。[削除]**を選択** すると、プライベート エンドポイントと関連付けられている NIC が削除されます。 FHIR リソースとパブリック ネットワークに対してすべてのプライベート エンドポイントを削除すると、アクセスは無効になり、FHIR サーバーに対する要求は行されません。

![プライベート エンドポイントの削除](media/private-link/private-link-delete.png)

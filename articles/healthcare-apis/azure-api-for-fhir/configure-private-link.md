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
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779260"
---
# <a name="configure-private-link"></a>プライベート リンクを構成する

プライベート リンクを使用すると、プライベート エンドポイント経由で Azure API for FHIR にアクセスできます。プライベート エンドポイントは、仮想ネットワークのプライベート IP アドレスを使用してプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート リンクを使用すると、パブリック ドメイン ネーム システム (DNS) を経由しなくても、VNet から Microsoft のサービスにファーストパーティ サービスとして安全にアクセスできます。 この記事では、Azure API for FHIR のプライベート エンドポイントを作成、テスト、および管理する方法について説明します。

>[!Note]
>Private Link を有効にすると、Private Link または Azure API for FHIR も、あるリソース グループまたはサブスクリプションから別のリソース グループまたはサブスクリプションに移動できなくなります。 移動するには、まずプライベート リンクを削除し、次に Azure API for FHIR を移動します。 移動が完了したら、新しいプライベート リンクを作成します。 Private Link を削除する前に、潜在的なセキュリティの問題を評価します。
>
>Azure API for FHIR の監査ログとメトリックのエクスポートが有効になっている場合は、ポータルから **[診断設定]** を使用してエクスポート設定を更新します。

## <a name="prerequisites"></a>前提条件

プライベート エンドポイントを作成する前に、いくつかの Azure リソースをまず作成しておく必要があります。

- リソース グループ – 仮想ネットワークとプライベート エンドポイントを含める Azure リソース グループ。
- Azure API for FHIR – プライベート エンドポイントの背後に配置する FHIR リソース。
- 仮想ネットワーク – クライアント サービスとプライベート エンドポイントが接続される VNet。

詳細については、「[Private Link のドキュメント](../../private-link/index.yml)」を参照してください。

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

プライベート エンドポイントを作成するために、FHIR リソースに対するロールベースのアクセス制御 (RBAC) のアクセス許可を持つ開発者は、Azure portal、[Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)、または [Azure CLI](../../private-link/create-private-endpoint-cli.md) を使用できます。 この記事では、Azure portal を使用する手順について説明します。 プライベート DNS ゾーンの作成と構成を自動化するため、Azure portal を使用することをお勧めします。 詳細については、[Private Link のクイック スタート ガイド](../../private-link/create-private-endpoint-portal.md)を参照してください。

プライベート エンドポイントを作成するには、2 つの方法があります。 自動承認フローを使用すると、FHIR リソースに対する RBAC アクセス許可を持つユーザーは、承認を受ける必要なく、プライベート エンドポイントを作成できます。 手動承認フローを使用すると、FHIR リソースに対するアクセス許可を持たないユーザーは、プライベート エンドポイントを FHIR リソースの所有者に承認してもらうよう要求できます。

> [!NOTE]
> 承認されたプライベート エンドポイントが Azure API for FHIR に対して作成されると、そのエンドポイントへのパブリック トラフィックは自動的に無効になります。 

### <a name="auto-approval"></a>自動承認

新しいプライベート エンドポイントのリージョンが、使用している仮想ネットワークのリージョンと同じであることを確認します。 FHIR リソースのリージョンは異なっていてもかまいません。

![Azure portal の [基本] タブ](media/private-link/private-link-portal2.png)

[リソースの種類] には、**Microsoft.HealthcareApis/services** を検索して選択します。 [リソース] には、FHIR リソースを選択します。 [ターゲット サブリソース] には、 **[FHIR]** を選択します。

![Azure portal の [リソース] タブ](media/private-link/private-link-portal1.png)

既存のプライベート DNS ゾーンが設定されていない場合は、 **(New)privatelink.azurehealthcareapis.com** を選択します。 プライベート DNS ゾーンが既に構成されている場合は、一覧からそれを選択できます。 これは、**privatelink.azurehealthcareapis.com** の形式である必要があります。

![Azure Portal の [構成] タブ](media/private-link/private-link-portal3.png)

デプロイが完了したら、 **[プライベート エンドポイント接続]** タブに戻ります。ここでは、接続状態が "**承認済み**" と表示されます。

### <a name="manual-approval"></a>手動承認

手動承認の場合は、[リソース] の下の 2 つ目のオプションである [リソース ID またはエイリアスを使って Azure リソースに接続します] を選択します。 [ターゲット サブリソース] で、自動承認と同様に「fhir」と入力します。

![手動承認](media/private-link/private-link-manual.png)

デプロイが完了したら、[プライベート エンドポイント接続] タブに戻ります。ここでは、接続の承認、拒否、または削除を行うことができます。

![オプション](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>プライベート エンドポイントをテストする

パブリック ネットワーク アクセスを無効にした後、FHIR サーバーがパブリック トラフィックを受信していないことを確認するには、お使いのコンピューターからサーバーの /metadata エンドポイントを選択します。 "403 Forbidden" (403 許可されていません) という応答を受け取るはずです。 


> [!NOTE]
> パブリック ネットワーク アクセス フラグを更新してからパブリック トラフィックがブロックされるまでに、最大 5 分かかることがあります。

プライベート エンドポイントからサーバーへトラフィックが送信されることを確認するには、次のようにします。

1. プライベート エンドポイントが構成されている仮想ネットワークとサブネットに接続されている仮想マシン (VM) を作成します。 VM からのトラフィックで確実にプライベート ネットワークのみが使用されるようにするには、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信インターネット トラフィックを無効にします。
2. RDP で VM に接続します。
3. VM から FHIR サーバーの /metadata エンドポイントにアクセスします。 応答として機能ステートメントを受け取るはずです。

## <a name="manage-private-endpoint"></a>マネージド プライベート エンドポイント

### <a name="view"></a>表示

プライベート エンドポイントとそれに関連付けられているネットワーク インターフェイス コントローラー (NIC) は、それらが作成されたリソース グループから Azure portal で確認できます。

![リソースでの表示](media/private-link/private-link-view.png)

### <a name="delete"></a>削除

プライベート エンドポイントは、Azure portal の **[概要]** ブレードを使用するか、 **[ネットワーク プライベート エンドポイント接続]** タブの **[削除]** オプションを選択することによってのみ削除できます。 **[削除]** を選択すると、プライベート エンドポイントだけでなく、関連付けられている NIC も削除されます。 FHIR リソースへのすべてのプライベート エンドポイントとパブリック ネットワークを削除すると、アクセスは無効になり、FHIR サーバーに対する要求は行われません。

![プライベート エンドポイントの削除](media/private-link/private-link-delete.png)

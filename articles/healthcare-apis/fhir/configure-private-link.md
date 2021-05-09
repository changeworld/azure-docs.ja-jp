---
title: Azure API for FHIR 用のプライベート リンク
description: この記事では、Azure API for FHIR サービス用のプライベート エンドポイントを設定する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 04/29/2021
ms.author: zxue
ms.openlocfilehash: 248d499d166c6e397ef422b5ff653709b8b075e9
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108733456"
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

## <a name="disable-public-network-access"></a>パブリック ネットワーク アクセスの無効化

FHIR リソースのプライベートエンドポイントを作成しても、そのエンドポイントへのパブリックトラフィックが自動的に無効になることはありません。 これを行うには、FHIR リソースを更新して、新しい "パブリックアクセス" プロパティを "Enabled" から "Disabled" に設定する必要があります。 パブリック ネットワーク アクセスを無効にすると、適切に構成されたプライベート エンドポイントからのものではない FHIR サービスへのすべての要求が拒否されますので注意してください。 プライベート エンドポイントからのトラフィックのみが許可されます。

:::image type="content" source="media/private-link/private-link-disable.png" alt-text="パブリックネットワークアクセスを無効にします。":::

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

プライベートエンドポイントを作成するために、FHIR リソースに対するロールベースのアクセス制御 (RBAC) アクセス許可を持つ開発者は、Azure portal、 [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)、または [Azure CLI](../../private-link/create-private-endpoint-cli.md)を使用できます。 この記事では、Azure portal を使用する手順について説明します。 プライベート DNS ゾーンの作成と構成を自動化するため、Azure portal を使用することをお勧めします。 詳細については、「 [プライベートリンククイックスタートガイド](../../private-link/create-private-endpoint-portal.md)」を参照してください。

プライベート エンドポイントを作成するには、2 つの方法があります。 自動承認フローを使用すると、FHIR リソースに対する RBAC アクセス許可を持つユーザーは、承認を受ける必要なく、プライベート エンドポイントを作成できます。 手動承認フローを使用すると、FHIR リソースに対するアクセス許可を持たないユーザーは、プライベート エンドポイントを FHIR リソースの所有者に承認してもらうよう要求できます。

### <a name="auto-approval"></a>自動承認

新しいプライベートエンドポイントのリージョンが仮想ネットワークのリージョンと同じであることを確認します。 FHIR リソースのリージョンは異なっていてもかまいません。

![Azure portal の [基本] タブ](media/private-link/private-link-portal2.png)

リソースの種類については、 **HealthcareApis/services** を検索して選択します。 リソースに対して、[FHIR] リソースを選択します。 [ターゲットサブリソース] で [ **Fhir**] を選択します。

![Azure portal の [リソース] タブ](media/private-link/private-link-portal1.png)

既存のプライベート DNS ゾーンが設定されていない場合は、[ **(新規) privatelink azurehealthcareapis** を選択します。 プライベート DNS ゾーンが既に構成されている場合は、一覧からそれを選択できます。 **Privatelink.azurehealthcareapis.com** の形式である必要があります。

![Azure Portal の [構成] タブ](media/private-link/private-link-portal3.png)

デプロイが完了したら、[ **プライベートエンドポイント接続** ] タブに戻り、接続状態として **承認** されていることを確認できます。

### <a name="manual-approval"></a>手動承認

手動承認の場合は、[リソース] の下の 2 つ目のオプションである [リソース ID またはエイリアスを使って Azure リソースに接続します] を選択します。 [ターゲット サブリソース] で、自動承認と同様に「fhir」と入力します。

![手動承認](media/private-link/private-link-manual.png)

デプロイが完了したら、[プライベート エンドポイント接続] タブに戻ります。ここでは、接続の承認、拒否、または削除を行うことができます。

![オプション](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>プライベート エンドポイントをテストする

パブリックネットワークアクセスを無効にした後に FHIR サーバーがパブリックトラフィックを受信しないようにするには、コンピューターからサーバーのメタデータエンドポイントを選択します。 "403 Forbidden" (403 許可されていません) という応答を受け取るはずです。 


> [!NOTE]
> パブリックトラフィックがブロックされる前に、パブリックネットワークアクセスフラグを更新してから最大5分かかることがあります。

プライベートエンドポイントがサーバーにトラフィックを送信できるようにするには、次のようにします。

1. プライベートエンドポイントが構成されている仮想ネットワークとサブネットに接続された仮想マシン (VM) を作成します。 VM からのトラフィックがプライベートネットワークのみを使用するようにするには、ネットワークセキュリティグループ (NSG) ルールを使用して送信インターネットトラフィックを無効にします。
2. RDP で VM に接続します。
3. VM から FHIR サーバーのメタデータエンドポイントにアクセスします。 応答として機能ステートメントを受け取る必要があります。

## <a name="manage-private-endpoint"></a>マネージド プライベート エンドポイント

### <a name="view"></a>表示

プライベートエンドポイントと関連付けられているネットワークインターフェイスコントローラー (NIC) は、作成されたリソースグループから Azure portal に表示されます。

![リソースでの表示](media/private-link/private-link-view.png)

### <a name="delete"></a>削除

プライベートエンドポイントを削除できるのは、[**概要**] ブレードの Azure portal から、または [**ネットワークのプライベートエンドポイント接続**] タブで [**削除**] オプションを選択した場合のみです。[**削除**] を選択すると、プライベートエンドポイントと関連付けられている NIC が削除されます。 FHIR リソースとパブリックネットワークに対するすべてのプライベートエンドポイントを削除すると、アクセスは無効になり、FHIR サーバーに対して要求が行われません。

![プライベート エンドポイントの削除](media/private-link/private-link-delete.png)

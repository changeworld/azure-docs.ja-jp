---
title: Azure Virtual Network Manager によって適用された構成の表示 (プレビュー)
description: Azure Virtual Network Manager によって適用された構成を表示する方法について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0d0f0d0ed5eb100aff4df02a65ab4902da1b7c16
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091178"
---
# <a name="view-configurations-applied-by-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager によって適用された構成の表示 (プレビュー)

Azure Virtual Network Manager には、構成が正しく適用されていることを確認するためのさまざまな方法がいくつかあります。 この記事では、仮想ネットワーク レベルと仮想マシン レベルの両方で適用された構成を確認する方法について説明します。 アクティビティ ログに表示される操作も確認します。

## <a name="virtual-network-visibility"></a>仮想ネットワークの可視性

Virtual Network Manager によって構成がデプロイされると、仮想ネットワーク リソースから適用された構成を表示できるようになります。 

1. 仮想ネットワーク リソースに移動し、 **[ネットワーク マネージャー]** を *[設定]* から選びます。 [接続] タブには、仮想ネットワークが関連付けられているすべての接続構成が表示されます。 

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-connectivity.png" alt-text="仮想ネットワークに関連付けられている接続構成のスクリーンショット。":::

2. **[SecurityAdmin]** タブを選択すると、仮想ネットワークに現在適用されているセキュリティ規則がすべて表示されます。

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-security.png" alt-text="仮想ネットワークに関連付けられているセキュリティ規則のスクリーンショット。":::

## <a name="virtual-machine-visibility"></a>仮想マシンの可視性

仮想マシン レベルでは、Virtual Network Manager によって適用されたセキュリティ規則と、接続構成の有効なルートを表示できます。

### <a name="applied-security-rules"></a>適用されるセキュリティ規則

1. Virtual Network Manager によって構成が適用されている仮想ネットワーク内の仮想マシンに移動します。 次に、左側のメニュー ウィンドウの *[設定]* で **[ネットワーク]** を選択します。

    :::image type="content" source="./media/how-to-view-applied-configurations/virtual-machine.png" alt-text="仮想マシンの概要ページのスクリーンショット。":::

1. 受信ネットワーク セキュリティ グループのリストと、Virtual Network Manager によって適用される受信セキュリティ規則のセクションが表示されます。

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-inbound-rules.png" alt-text="仮想マシンの送信セキュリティ規則のスクリーンショット。":::

1. **[送信ポート規則]** タブを選択して、仮想マシンの送信セキュリティ規則を表示します。

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-outbound-rules.png" alt-text="仮想マシンの受信セキュリティ規則のスクリーンショット。":::

### <a name="effective-routes"></a>有効なルート

1. 適用された接続構成の有効なルートを表示するには、仮想マシンの *[ネットワーク]* 設定でネットワーク インターフェイス名を選択します。

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="仮想マシンのネットワーク インターフェイス カードを選択しているスクリーンショット。":::

1. 次に、 *[サポート + トラブルシューティング]* で、 **[有効なルート]** を選択します。

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface.png" alt-text="VM ネットワーク インターフェイス カードの有効なルート ボタンのスクリーンショット。":::

1. ネクスト ホップの種類が *ConnectedGroup* であるルートは、メッシュ構成の一部か、ネットワーク グループに対して [*直接接続*](concept-connectivity-configuration.md#direct-connectivity)が有効になっている場合に行います。 ハブとスポークの仮想ネットワーク間のルートは、ネクスト ホップの種類が *VNetPeering* または *GlobalVNetPeering* として表示されます。

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-routes.png" alt-text="接続されているグループとハブ ルートを示す有効なルートのスクリーンショット。" lightbox="./media/how-to-view-applied-configurations/effective-routes-expanded.png":::

    > [!NOTE]
    > ハブ仮想ネットワークのアドレス空間は、*ConnectedGroup* にも **含まれています**。 そのため、ハブ仮想ネットワークとスポーク仮想ネットワークの間で仮想ネットワーク ピアリングが失敗した場合でも、それらは接続されたグループに属しているため、相互に通信できます。
    > 

### <a name="effective-security-rules"></a>有効なセキュリティ ルール

1. 適用されたセキュリティ規則構成の有効なセキュリティ規則を表示するには、仮想マシンの *[ネットワーク]* 設定でネットワーク インターフェイス名を選択します。

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="セキュリティ規則に仮想マシンのネットワーク インターフェイス カードを選択しているスクリーンショット。":::

1. 次に、 *[サポート + トラブルシューティング]* で、 **[有効なセキュリティ規則]** を選択します。

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface-security-rules.png" alt-text="VM ネットワーク インターフェイス カードの有効なセキュリティ規則ボタンのスクリーンショット。":::

1. Azure Virtual Network Manager の名前を選択して、仮想マシンに関連付けられているセキュリティ管理者規則を確認します。

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-security-rules.png" alt-text="仮想マシンに関連付けられている有効なセキュリティ規則のスクリーンショット。":::

## <a name="activity-log"></a>アクティビティ ログ

Azure Virtual Network Manager リソースのアクティビティ ログを表示して、自分またはネットワーク管理者が行った変更を確認できます。 アクティビティ ログを表示するには、Azure portal のネットワーク マネージャー リソースに移動します。 左ウィンドウのメニューで **[アクティビティ ログ]** を選択します。 必要に応じて、 *[Timespan]* を調整し、操作のリストを絞り込むフィルターを追加します。 Azure portal の上部でサービスを検索して、 *[アクティビティ ログ]* を表示することもできます。

:::image type="content" source="./media/how-to-view-applied-configurations/activity-log.png" alt-text="Network Manager の [アクティビティ ログ] ページのスクリーンショット。":::

### <a name="list-of-operations"></a>操作の一覧

次の一覧には、アクティビティ ログに表示される操作が含まれます。

| 名前 | 説明 |
| ---- | ----------- |
| Commit | 構成の展開はリージョンにコミットされています。 |
| ConnectivityConfiguration の削除 | ネットワーク マネージャーから接続構成を削除する。 |
| NetworkGroups の削除 | ネットワーク マネージャーからネットワーク グループを削除する。|
| ルールの更新 | 規則コレクションから規則を削除する。 |
| RuleCollections の削除 | セキュリティ管理者の構成から規則コレクションを削除する。 |
| SecurityAdminConfigurations の削除 | ネットワーク マネージャーからセキュリティ管理者の構成を削除する。 |
| ListDeploymentStatus | 接続またはセキュリティ管理者の構成の展開状態を表示する。 |
| ListActiveConnectivityConfiguration | 仮想ネットワークに適用される接続構成のリストを表示する。|
| ListActiveSecurityAdminRules | 仮想ネットワークに適用されるセキュリティ管理者構成のリストを表示する。 |
| ConnectivityConfiguration の書き込み。 | 新しい接続構成の作成。 |
| NetworkGroups の書き込み | 新しいネットワーク グループを作成する。 |
| NetworkManager の書き込み | Azure Virtual Network Manager インスタンスを作成する。 |
| ルールの書き込み | 規則コレクションに追加する新しいセキュリティ規則を作成する。 |
| RuleCollections の書き込み | セキュリティ管理者の構成に追加する新しい規則コレクションを作成する。 |
| SecurityAdminConfiguration の書き込み | 新しいセキュリティ管理者構成を作成する。 |

## <a name="next-steps"></a>次のステップ

- Azure portal を使用して [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成します。
- よく寄せられる質問については、[Network Manager の FAQ](faq.md) を参照してください。

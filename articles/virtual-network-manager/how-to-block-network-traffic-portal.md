---
title: Azure Virtual Network Manager (プレビュー) を使用してネットワーク トラフィックをブロックする方法 - Azure portal
description: Azure portal で Azure Virtual Network Manager のセキュリティ規則を使用してネットワーク トラフィックをブロックする方法について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 20adb2a748f839d63122a1fc5904746a29af33ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092662"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-portal"></a>Azure Virtual Network Manager (プレビュー) を使用してネットワーク トラフィックをブロックする方法 - Azure portal

この記事では、RDP ポート 3389 で受信ネットワーク トラフィックをブロックするセキュリティ管理規則を作成して規則コレクションに追加する方法について説明します。 詳細については、[セキュリティ管理規則](concept-security-admins.md)に関するページをご覧ください。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

セキュリティ管理規則の構成を開始する前に、次の手順が実行済みであることを確認してください。

* [セキュリティ管理規則](concept-security-admins.md)の各要素を理解している。
* [Azure Virtual Network Manager インスタンス](create-virtual-network-manager-portal.md)を作成した。

## <a name="create-a-securityadmin-configuration"></a>SecurityAdmin 構成を作成する

1. [*設定*] で **[構成]** を選択してから、 **[+ 構成の追加]** を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-admin.png" alt-text="セキュリティ管理構成の追加のスクリーンショット。":::

1. ドロップダウン メニューから **[SecurityAdmin]** を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-admin-drop-down.png" alt-text="[構成の追加] ドロップダウン メニューのスクリーンショット。":::

1. このセキュリティ構成を識別する [*名前*] を入力します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-configuration-name.png" alt-text="セキュリティ構成名フィールドのスクリーンショット。":::

## <a name="add-a-rule-collection"></a>規則コレクションを追加する

1. 一連の規則を作成するため、 **[+ 規則コレクションの追加]** を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-collection-button.png" alt-text="[規則コレクションの追加] ボタンのスクリーンショット。":::

1. この規則コレクションを識別する [*名前*] を入力し、一連の規則を適用する [*ネットワーク グループ*] を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/rule-collection-target.png" alt-text="規則コレクション名とターゲット ネットワーク グループのスクリーンショット。":::

## <a name="add-a-security-rule"></a>セキュリティ規則を追加する

1. [*規則コレクションの追加*] ページで、 **[+ 規則の追加]** を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-button.png" alt-text="[規則の追加] ボタンのスクリーンショット。":::

1. 次の情報を入力または選択してから、 **[追加]** を選択して規則コレクションに規則を追加します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule.png" alt-text="[規則の追加] ページのスクリーンショット。":::

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 規則名として「**Deny_RDP**」と入力します。 |
    | 説明 | 規則に関する説明を入力します。 |
    | 優先順位* | 規則の優先順位を決定する値 (0-99) を入力します。 数値が小さいほど優先順位が高くなります。 この例では「**1**」と入力します|
    | アクション* | トラフィックをブロックする **[拒否]** を選択します。 詳細については、「[アクション](concept-security-admins.md#action)」を参照してください
    | 方向* | この規則を使用して受信トラフィックを拒否するため、 **[受信]** を選択します。 |
    | プロトコル* | **[TCP]** プロトコルを選択します。 HTTP と HTTPS は TCP ポートです。 |
    | 送信元の種類 | ソースの種類として **[IP アドレス]** または **[サービス タグ]** のいずれかを選択します。 |
    | ソース IP アドレス | このフィールドは、ソースの種類として [*IP アドレス*] を選択したときに表示されます。 CIDR 表記を使用して IPv4 または IPv6 のアドレスまたは範囲を入力します。 複数のアドレスまたはアドレスのブロックを定義する場合は、コンマを使用して区切ります。 この例では空白のままにします。|
    | 発信元サービス タグ | このフィールドは、ソースの種類として [*サービス タグ*] を選択したときに表示されます。 ソースとして指定するサービスのサービス タグを選択します。 サポートされているタグの一覧については、「[利用可能なサービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)」を参照してください。 |
    | 送信元ポート | 1 つのポート番号またはポート範囲 (1024-65535 など) を入力します。 複数のポートまたはポート範囲を定義する場合は、コンマを使用して区切ります。 任意のポートを指定するには、「*」と入力します。 この例では空白のままにします。|
    | 変換先の型 | 宛先の種類として **[IP アドレス]** または **[サービス タグ]** のいずれかを選択します。 |
    | 送信先 IP アドレス | このフィールドは、宛先の種類として [*IP アドレス*] を選択したときに表示されます。 CIDR 表記を使用して IPv4 または IPv6 のアドレスまたは範囲を入力します。 複数のアドレスまたはアドレスのブロックを定義する場合は、コンマを使用して区切ります。 |
    | 宛先サービス タグ | このフィールドは、宛先の種類として [*サービス タグ*] を選択したときに表示されます。 宛先として指定するサービスのサービス タグを選択します。 サポートされているタグの一覧については、「[利用可能なサービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)」を参照してください。 |
    | 宛先ポート | 1 つのポート番号またはポート範囲 (1024-65535 など) を入力します。 複数のポートまたはポート範囲を定義する場合は、コンマを使用して区切ります。 任意のポートを指定するには、「*」と入力します。 この例では「**3389**」と入力します。 |

1. 規則コレクションにさらに規則を追加する場合は、手順 1 から 3 を繰り返します。

1. 必要なすべての規則の作成が完了したら、 **[保存]** を選択して、規則コレクションをセキュリティ管理構成に追加します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/save-rule-collection.png" alt-text="規則コレクション内の規則のスクリーンショット。":::

1. その後、 **[追加]** を選択して構成を作成します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-configuration.png" alt-text="セキュリティ構成を作成するための [追加] ボタンのスクリーンショット。":::

## <a name="deploy-the-security-admin-configuration"></a>セキュリティ管理構成をデプロイする

新しいセキュリティ管理構成を作成したばかりの場合は、必ずこの構成をデプロイして、ネットワーク グループ内の仮想ネットワークに適用してください。

1. [*設定*] で **[デプロイ]** を選択してから、 **[構成のデプロイ]** を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-configuration.png" alt-text="[構成のデプロイ] ボタンのスクリーンショット。":::

1. 構成の種類として **[SecurityAdmin]** を選択し、前のセクションで作成した構成を選択します。 次に、この構成をデプロイするリージョンを選択し、 **[デプロイ]** を選択します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-security-configuration.png" alt-text="[セキュリティ構成のデプロイ] ページのスクリーンショット。":::

1. **[OK]** を選択して、既存の構成を上書きしてセキュリティ管理構成をデプロイすることを確認します。

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="セキュリティ構成のデプロイに関する確認メッセージのスクリーンショット。":::

## <a name="update-existing-security-admin-configuration"></a>既存のセキュリティ管理構成を更新する

* 更新するセキュリティ構成を、静的メンバーを含むネットワーク グループに適用する場合は、構成をもう一度デプロイして有効にする必要があります。
* ネットワーク グループ内の動的メンバーには、セキュリティ管理構成が自動的に適用されます。

## <a name="verify-security-admin-rules"></a>セキュリティ管理規則を確認する

セキュリティ管理者規則を適用した仮想ネットワークのいずれかに含まれる仮想マシンの **[ネットワーク]** 設定にアクセスします。 ない場合は、仮想ネットワークのいずれかにテスト用仮想マシンをデプロイします。 これで、ネットワーク セキュリティ グループ規則の下に、Azure Virtual Network Manager によって適用されたセキュリティ規則に関する新しいセクションが表示されます。

:::image type="content" source="./media/how-to-block-network-traffic-portal/vm-security-rules.png" alt-text="仮想マシンのネットワーク設定の下に表示されたセキュリティ管理規則のスクリーンショット。" lightbox="./media/how-to-block-network-traffic-portal/vm-security-rules-expanded.png":::

## <a name="next-steps"></a>次のステップ

[セキュリティ管理規則](concept-security-admins.md)の詳細を確認する

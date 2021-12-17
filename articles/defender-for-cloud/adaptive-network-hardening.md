---
title: Microsoft Defender for Cloud でのアダプティブ ネットワークのセキュリティ強化 | Microsoft Docs
description: 実際のトラフィック パターンを使用してネットワーク セキュリティ グループ (NSG) のルールを強化し、セキュリティ体制さらに向上させる方法について説明します。
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: b26068a7e8400fa6167dbe91d2e7db039bcc68d9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525571"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>アダプティブ ネットワークのセキュリティ強化により、ネットワークのセキュリティ体制を向上させる

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

アダプティブ ネットワークのセキュリティ強化は、Microsoft Defender for Cloud のエージェントレス機能です。このネットワークのセキュリティ強化ツールを利用するために、何かをコンピューターにインストールする必要はありません。

このページでは、Defender for Cloud でアダプティブ ネットワークのセキュリティ強化を構成および管理する方法について説明します。

## <a name="availability"></a>可用性
|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要|
|必要なロールとアクセス許可:|コンピューターの NSG の書き込みアクセス許可|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="what-is-adaptive-network-hardening"></a>アダプティブ ネットワークのセキュリティ強化機能とは
[ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md) を適用してリソースとの間でやり取りされるトラフィックをフィルター処理することにより、ネットワーク セキュリティ ポスチャを向上させることができます。 ただし、NSG を通過する実際のトラフィックが、定義される NSG ルールのサブセットとなる場合もあります。 このような場合は、実際のトラフィック パターンに基づいて NSG ルールを強化することによって、セキュリティ ポスチャをさらに向上させることができます。

アダプティブ ネットワークのセキュリティ強化機能によって、NSG ルールをさらに強化するための推奨事項が提供されます。 実際のトラフィック、既知の信頼された構成、脅威インテリジェンス、および侵害に関するその他のインジケーターを考慮する機械学習アルゴリズムを使用し、特定の IP/ポート タプルからのトラフィックのみを許可する推奨事項を提示します。

たとえば、既存の NSG ルールがポート 22 の 140.20.30.10/24 からのトラフィックを許可するとします。 トラフィック分析に基づき、アダプティブ ネットワークのセキュリティ強化では、140.23.30.10/29 からのトラフィックを許可する範囲を絞り込み、またそのポートへの他のすべてのトラフィックを拒否することを推奨される場合があります。 サポートされているポートの完全な一覧については、FAQ エントリ「[サポートされているポート](#which-ports-are-supported)」を参照してください。

## <a name="view-hardening-alerts-and-recommended-rules"></a>セキュリティ強化に関するアラートおよび推奨されるルールを表示する

1. Defender for Cloud のメニューで、 **[ワークロード保護]** ダッシュボードを開きます。
1. [アダプティブ ネットワーク強化] タイル (1)、またはアダプティブ ネットワークのセキュリティ強化に関連する分析情報パネル項目 (2) を選択します。 

    :::image type="content" source="./media/adaptive-network-hardening/traffic-hardening.png" alt-text="アダプティブ ネットワーク セキュリティ強化ツールへのアクセス。" lightbox="./media/adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > 分析情報パネルには、現在、アダプティブ ネットワークのセキュリティ強化によって保護されている VM の割合が表示されます。 

1. **[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある]** 推奨事項の詳細ページが開き、ネットワーク VM が次の 3 つのタブにグループされます。
   * **異常なリソース**:アダプティブ ネットワークのセキュリティ強化アルゴリズムを実行することによってトリガーされた推奨事項とアラートが存在する VM。 
   * **正常なリソース**:アラートと推奨事項がない VM。
   * **スキャンされていないリソース**:以下のいずれかの理由でアダプティブ ネットワークのセキュリティ強化アルゴリズムを実行できない VM。
      * **VM がクラシック VM である**:Azure Resource Manager VM のみがサポートされています。
      * **使用できる十分なデータがない**: トラフィック強化に関する正確な推奨事項を生成するには、Defender for Cloud に少なくとも 30 日間のトラフィック データが必要です。
      * **VM が Microsoft Defender for servers によって保護されていない**: この機能の対象となるのは、[Microsoft Defender for servers](defender-for-servers-introduction.md) で保護されている VM のみです。

    :::image type="content" source="./media/adaptive-network-hardening/recommendation-details-page.png" alt-text="推奨事項 [アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある] の詳細ページ。":::

1. **[異常なリソース]** タブから、そのアラートと適用する推奨されるセキュリティ強化ルールを表示する VM を選択します。

    - **[ルール]** タブには、アダプティブ ネットワークのセキュリティ強化機能が推奨するルールがリストされます。
    - **[アラート]** タブには、推奨されるルールで許可されている IP 範囲内ではないリソースを通過するトラフィックのために生成されたアラートが一覧表示されます。

1. 必要に応じて、ルールを編集します。

    - [ルールを変更する](#modify-rule)
    - [規則を削除する](#delete-rule) 
    - [ルールの追加](#add-rule)

3. NSG に適用するルールを選択し、 **[適用]** を選択します。

    > [!TIP]
    > 許可されているソース IP の範囲が "なし" である場合は、推奨されるルールが "*拒否*" ルールであることを意味します。それ以外の場合は、"*許可*" ルールです。

    :::image type="content" source="./media/adaptive-network-hardening/hardening-alerts.png" alt-text="アダプティブ ネットワークのセキュリティ強化機能ルールの管理":::

      > [!NOTE]
      > 適用されるルールは、VM を保護する NSG に追加されます。 (VM は、その NIC に関連付けられている NSG、VM が置かれているサブネット、またはその両方によって保護されます)

## <a name="modify-a-rule"></a>ルールの変更  <a name ="modify-rule"> </a>

推奨されたルールのパラメーターを変更することもできます。 たとえば、推奨された IP 範囲を変更することができます。

アダプティブ ネットワークのセキュリティ強化機能のルールを変更するための重要なガイドライン:

- **許可** ルールを **拒否** ルールに変更することはできません。 

- **許可** ルールのパラメーターのみを変更できます。 

    "拒否" ルールの作成と変更は、NSG で直接実行されます。 詳細については、「[ネットワーク セキュリティ グループを作成、変更、削除](../virtual-network/manage-network-security-group.md)」をご覧ください。

- **"すべてのトラフィックを拒否"** ルールは、ここで表示される唯一の "拒否" ルールのタイプで、変更することはできません。 ただし、そのルールを削除することはできます (「[ルールの削除](#delete-rule)」をご覧ください)。 この種類のルールの詳細については、FAQ エントリ「["すべてのトラフィックを拒否" ルールはいつ使用する必要がありますか?](#when-should-i-use-a-deny-all-traffic-rule)」を参照してください。

アダプティブ ネットワークのセキュリティ強化機能ルールを変更するには:

1. ルールのパラメーターの一部を変更するには、 **[ルール]** タブで、ルールの行の最後にある 3 つのドット (...) を選択し、 **[編集]** を選択します。

   ![S ルールの編集。](./media/adaptive-network-hardening/edit-hard-rule.png)

1. **[ルールの編集]** ウィンドウで、変更する詳細を更新し、 **[保存]** を選択します。

   > [!NOTE]
   > **[保存]** を選択すると、ルールは正常に変更されます。 *ただし、NSG にはまだ適用されていません。* そのルールを適用するには、一覧でそのルールを選択し、 **[適用]** を選択する必要があります (次の手順で説明します)。

   ![[保存] を選択。](./media/adaptive-network-hardening/edit-hard-rule3.png)

3. 更新されたルールを適用するには、一覧から更新されたルールを選択し、 **[適用]** を選択します。

    ![ルールの適用。](./media/adaptive-network-hardening/enforce-hard-rule.png)

## <a name="add-a-new-rule"></a>新しいルールの追加 <a name ="add-rule"> </a>

Defender for Cloud によって推奨されていない "許可" ルールを追加することができます。

> [!NOTE]
> ここでは、"許可" ルールのみを追加できます。 "拒否" ルールを追加する場合は、NSG で直接行うことができます。 詳細については、「[ネットワーク セキュリティ グループを作成、変更、削除](../virtual-network/manage-network-security-group.md)」をご覧ください。

アダプティブ ネットワークのセキュリティ強化機能ルールを追加するには:

1. 上部のツール バーで、 **[ルールの追加]** を選択します。

   ![ルールの追加。](./media/adaptive-network-hardening/add-hard-rule.png)

1. **[新しいルール]** ウィンドウで、詳細を入力し、 **[追加]** を選択します。

   > [!NOTE]
   > **[追加]** を選択すると、ルールは正常に追加され、その他の推奨されているルールと一緒に一覧表示されます。 ただし、NSG にはまだ "*適用*" されていません。 そのルールをアクティブにするには、一覧でそのルールを選択し、 **[適用]** を選択する必要があります (次の手順で説明します)。

3. 新しいルールを適用するには、一覧から新しいルールを選択し、 **[適用]** を選択します。

    ![ルールの適用。](./media/adaptive-network-hardening/enforce-hard-rule.png)


## <a name="delete-a-rule"></a>ルールを削除する <a name ="delete-rule"> </a>

現在のセッションの推奨されるルールが不要な場合は、削除できます。 たとえば、推奨されるルールを適用すると、正当なトラフィックがブロックされると判断することがあるかもしれません。

現在のセッションのアダプティブ ネットワークのセキュリティ強化機能ルールを削除するには:

- **[ルール]** タブで、ルールの行の最後にある 3 つのドット (...) を選択し、 **[削除]** を選択します。  

    ![ルールの削除。](./media/adaptive-network-hardening/delete-hard-rule.png)


## <a name="faq---adaptive-network-hardening"></a>FAQ - アダプティブ ネットワークのセキュリティ強化機能

- [サポートされているポート](#which-ports-are-supported)
- [アダプティブ ネットワークのセキュリティ強化機能に必要な前提条件または VM 拡張機能はありますか?](#are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening)

### <a name="which-ports-are-supported"></a>サポートされているポート

アダプティブ ネットワークのセキュリティ強化機能の推奨事項は、(UDP と TCP の両方において) 次の特定のポートでのみサポートされています。 

13、17、19、22、23、53、69、81、111、119、123、135、137、138、139、161、162、389、445、512、514、593、636、873、1433、1434、1900、2049、2301、2323、2381、3268、3306、3389、4333、5353、5432、5555、5800、5900、5900、5985、5986、6379、6379、7000、7001、7199、8081、8089、8545、9042、9160、9300、11211、16379、26379、27017、37215

### <a name="are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening"></a>アダプティブ ネットワークのセキュリティ強化機能に必要な前提条件または VM 拡張機能はありますか?

アダプティブ ネットワークのセキュリティ強化は、Microsoft Defender for Cloud のエージェントレス機能です。このネットワークのセキュリティ強化ツールを利用するために、何かをコンピューターにインストールする必要はありません。

### <a name="when-should-i-use-a-deny-all-traffic-rule"></a>"すべてのトラフィックを拒否" ルールはいつ使用する必要がありますか?

既存の NSG 構成に基づいて許可すべきトラフィックが Defender for Cloud で識別されなかった場合、アルゴリズムを実行した結果として、**すべてのトラフィックを拒否** ルールが推奨されます。 そのため、推奨されるルールは、指定されたポートに届くすべてのトラフィックを拒否します。 このタイプのルールの名前は、"*システム生成*" として表示されます。 このルールを適用した後は、NSG の実際の名前は、プロトコル、トラフィックの方向、"DENY"、およびランダムな数値から成る文字列になります。

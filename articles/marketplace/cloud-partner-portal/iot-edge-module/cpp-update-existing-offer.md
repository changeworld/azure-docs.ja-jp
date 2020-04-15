---
title: 既存の Azure IoT Edge モジュール プランの更新 | Azure Marketplace
description: Azure Marketplace で既存の IoT Edge モジュール プランを更新する方法。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 019711f35d249e6684013feddb6b453d509f66d4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985356"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>既存の IoT Edge モジュール プランの更新

>[!Important]
>2020 年 4 月 13 日以降、IoT Edge モジュールのパートナー センターへの移行管理が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[IoT Edge モジュール オファーを作成する](https://aka.ms/AzureCreateIoT)」の手順に従い、移行後のオファーを管理します。

この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)で IoT Edge モジュール プランを更新し、そのプランを再発行する際のさまざまな側面を説明します。

プランを更新する理由は以下のようにいくつか考えられます。

-  既存の SKU に新しい IoT Edge モジュール イメージ バージョンを追加する。
-  新しい SKU を追加する。
-  プランまたは 個々の SKU のマーケットプレース メタデータを更新する。

このような変更をサポートするため、ポータルには**比較**機能と**履歴**機能があります。  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>IoT Edge モジュール プランまたは SKU の許可されない変更

Azure Marketplace で IoT Edge モジュール プランがライブになった後に変更できないプランまたは SKU の属性があります。 次の設定は変更できません。

-  プランの**プラン ID** と**パブリッシャー ID**
-  既存の SKU の **SKU ID**
-  バージョン タグ (例: `1.0.1`)
-  既存の SKU に対する課金/ライセンス モデルの変更

## <a name="common-update-operations"></a>共通の更新操作

次の更新操作は共通です。

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>SKU の IoT Edge モジュール イメージ バージョンの更新

IoT Edge モジュール イメージがセキュリティ パッチ、追加機能などによって定期的に更新されるのは一般的です。 このシナリオでは、次の手順を使用して、SKU が参照する IoT Edge モジュール イメージを更新します。

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[SKU]** タブで、更新する IoT Edge モジュール イメージに関連付けられている SKU を選択します。

4.  **[Edge モジュール イメージ]** で、 **[+ New Image Version (新しいイメージ バージョンの追加)]** を選択し、新しい IoT Edge モジュール イメージを追加します。

5.  新しい IoT Edge モジュール **イメージ バージョン**を指定します。 イメージ バージョンでは、以前のバージョンと同じタグのガイドラインに従う必要があります。 バージョン タグの形式は X.Y.Z で、X、Y、Z は整数です。 指定した新しいバージョンが以前のすべてのバージョンよりも大きいことを確認します。

6.  **[発行]** を選択し、新しい IoT Edge モジュール バージョンを Azure Marketplace に発行するワークフローを開始します。

### <a name="add-a-new-sku"></a>新しい SKU の追加

次の手順に従って、新しい SKU をプランで使用できるようにします。 

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[SKU]** タブで、 **[Add new SKU (新しい SKU の追加)]** を選択し、ポップアップ ウィンドウに **[SKU ID]** を指定します。

4.  [IoT Edge モジュールの Azure Marketplace への発行](./cpp-publish-offer.md)に関する記事で説明している手順に従って、IoT Edge モジュールを再発行します。

5.  **[発行]** を選択し、新しい SKU を発行するワークフローを開始します。


### <a name="update-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新

次の手順に従って、プランに関連付けられている Marketplace メタデータを更新します。 (例: 会社名、ロゴなど)

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[Marketplace]** タブに移動します。[IoT Edge モジュールの Azure Marketplace への発行](./cpp-publish-offer.md)に関する記事の手順に従って、メタデータを変更します。

4.  **[発行]** を選択し、変更を発行するワークフローを開始します。

## <a name="compare-feature"></a>機能の比較

発行されているプランを変更する場合、 **[比較]** 機能を使用して、実行した変更を監査することができます。 

**比較機能を使用する方法**

1.  編集プロセスのどの時点でもプランの **[比較]** を選択できます。

    ![比較機能ボタン](./media/iot-edge-module-compare.png)


2.  マーケティングの資産やメタデータのバージョンを横に並べて表示します。


## <a name="history-of-publishing-actions"></a>発行操作の履歴

発行操作の履歴を表示するには、Cloud パートナー ポータルの左側のナビゲーション メニュー バーにある **[履歴]** タブを選択します。 Azure Marketplace プランの有効期間中に実行されたタイムスタンプ付きの操作を表示することができます。  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->

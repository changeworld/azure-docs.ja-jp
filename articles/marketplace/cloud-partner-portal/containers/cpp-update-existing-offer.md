---
title: 既存の Azure Containers オファーを更新する | Azure Marketplace
description: Azure Marketplace で既存のコンテナー オファーを更新する方法。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 650ef45e56184b67efba67810580d9d6e763a4d0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271269"
---
# <a name="update-an-existing-container-offer"></a>既存のコンテナー オファーを更新する

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure Container オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure Container オファーを作成する](https://aka.ms/CreateContainerOffer)」の手順に従って、移行されたオファーを管理します。

この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でコンテナー オファーを更新する際のさまざまな側面での手順を説明します。

プランを更新する理由は以下のようにいくつか考えられます。

-  既存の SKU に新しいコンテナー イメージ バージョンを追加する。
-  新しい SKU を追加する。
-  プランまたは 個々の SKU のマーケットプレース メタデータを更新する。

このような変更をサポートするため、ポータルには**比較**機能と**履歴**機能が用意されています。  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>コンテナー オファーまたは SKU に対する許可されない変更

コンテナー オファーや SKU には、Azure Marketplace でオファーがライブになった後は変更できない属性があります。 次の設定は変更できません。

-  プランの**プラン ID** と**パブリッシャー ID**
-  既存の SKU の **SKU ID**
-  バージョン タグ (例: `1.0.1`)
-  既存の SKU に対する課金/ライセンス モデルの変更

## <a name="common-update-operations"></a>共通の更新操作

次の更新操作は共通です。

### <a name="update-container-image-version-for-a-sku"></a>SKU のコンテナー イメージ バージョンの更新

コンテナー イメージがセキュリティ パッチ、追加機能などによって定期的に更新されるのは一般的です。 このシナリオでは、次の手順を使用して、SKU が参照するコンテナー イメージを更新します。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[すべてのプラン]** から、更新するプランを見つけます。
3. **[SKU]** タブで、更新するコンテナー イメージに関連付けられている SKU を選択します。
4. **[Container image]\(コンテナー イメージ)** で **[+ New Image Version]\(+ 新しいイメージ バージョン)** を選択し、新しいコンテナー イメージを追加します。
5. 新しいコンテナー **イメージ バージョン**を提供します。 イメージ バージョンでは、以前のバージョンと同じタグのガイドラインに従う必要があります。 バージョン タグの形式は X.Y.Z で、X、Y、Z は整数です。 指定した新しいバージョンが以前のすべてのバージョンよりも大きいことを確認します。
6. **[発行]** を選択し、新しいコンテナー イメージ バージョンを Azure Marketplace に発行するワークフローを開始します。

### <a name="add-a-new-sku"></a>新しい SKU の追加

次の手順に従って、新しい SKU をプランで使用できるようにします。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[すべてのプラン]** から、更新するプランを見つけます。
3. **[SKU]** タブで、 **[Add new SKU (新しい SKU の追加)]** を選択し、ポップアップ ウィンドウに **[SKU ID]** を指定します。
4. [コンテナー オファーを発行する](./cpp-publish-offer.md)ことに関するページで説明している手順を使用して、コンテナーを再パブリッシュします。
5. **[発行]** を選択し、新しい SKU を発行するワークフローを開始します。

### <a name="update-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新

次の手順に従って、プランに関連付けられている Marketplace メタデータを更新します。 (例: 会社名、ロゴなど)

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[すべてのオファー]** で、更新するオファーを見つけます。
3. **[Marketplace]** タブに移動します。[コンテナー オファーを発行する](./cpp-publish-offer.md)ことに関するオファーの記事の指示に従って、メタデータの変更を行います。
4. **[発行]** を選択し、変更を発行するワークフローを開始します。

## <a name="compare-feature"></a>機能の比較

発行されているオファーに変更を加える場合、 **[比較]** 機能を使用して、加えた変更を監査することができます。

### <a name="to-use-the-compare-feature"></a>比較機能を使用する方法:

1. 編集プロセスの任意の時点で、目的のオファーのために [比較] を選択します。
2. マーケティングの資産やメタデータのバージョンを横に並べて表示します。


## <a name="history-of-publishing-actions"></a>発行操作の履歴

発行操作の履歴を表示するには、Cloud パートナー ポータルの左側のナビゲーション メニュー バーにある **[履歴]** タブを選択します。 Azure Marketplace プランの有効期間中に実行されたタイムスタンプ付きの操作を表示することができます。

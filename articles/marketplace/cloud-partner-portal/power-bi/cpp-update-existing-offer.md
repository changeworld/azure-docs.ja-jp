---
title: Power BI アプリの既存のプランを更新する - Azure Marketplace | Microsoft Docs
description: Power BI アプリ プランが Microsoft AppSource Marketplace で発行されたら、そのプランを更新します。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665703"
---
# <a name="update-an-existing-power-bi-app-offer"></a>Power BI アプリの既存のプランを更新する

この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でご利用の Power BI アプリ プランを更新し、そのプランを再発行する際のさまざまな側面について説明します。  プランを更新する理由には、次のような一般的な理由があります。

- Power BI でアプリのコンテンツを更新して、新しくパッケージ化されたアプリから新しいインストールの URL を取得する
- プラン用の Marketplace メタデータを更新する (営業、マーケティング、またはサポート情報と資産)
 
このような変更をサポートするため、ポータルには**比較**機能と**履歴**機能があります。


## <a name="unpermitted-changes-to-offer"></a>プランの許可されない変更

Power BI アプリ プランの一部の属性は、AppSource にプランを発行した後に変更することはできません (主に**プラン ID** と**発行元 ID**)。


## <a name="common-update-operations"></a>共通の更新操作

Power BI アプリ プランには変更可能なさまざまな特性がありますが、次の操作が一般的なものです。


### <a name="update-app-content-in-power-bi"></a>Power BI でアプリ コンテンツを更新する

Power BI のアプリが、新しいコンテンツ、セキュリティ パッチ、追加機能などによって定期的に更新されるのは一般的です。 このようなシナリオでは、次の手順を使用して、新しいアプリ コンテンツのインストール URL を更新する必要があります。

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  **[技術情報]** タブで、新しいインストーラーの URL を入力します。
4.  **[発行]** をクリックし、新しいアプリのバージョンを AppSource に発行するワークフローを開始します。


### <a name="update-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新

次の手順に従って、プランに関連付けられている Marketplace メタデータ (会社名やロゴなど) を更新します。

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  **[電子店舗の詳細]** タブに移動して [Power BI アプリの [電子店舗の詳細] タブ](./cpp-storefront-details-tab.md)の指示に従い、メタデータを変更します。
4.  **[発行]** をクリックし、変更を発行するワークフローを開始します。


## <a name="compare-feature"></a>機能の比較

既に発行されているプランを変更する場合、**[比較]** 機能を使用して加えられた変更を監査することができます。 この機能を使用するには:

1.  編集プロセス中の任意のタイミングで、プランの **[比較]** をクリックします。

    ![比較機能ボタン](./media/compare-feature-button.png)

2.  マーケティングの資産やメタデータのバージョンを横に並べて表示します。


## <a name="history-of-publishing-actions"></a>発行操作の履歴

発行操作の履歴を表示するには、Cloud パートナー ポータルの左側のナビゲーション メニューバーにある **[履歴]** タブをクリックします。 ここで、AppSource プランの有効期間中に実行されたタイムスタンプ付きの操作を表示することができます。


## <a name="next-steps"></a>次の手順

自分の Marketplace の顧客と利用状況について分析情報を提供するには、[Cloud パートナー ポータル](https://cloudpartner.azure.com/#insights)の [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) の機能を定期的に使用する必要があります。  

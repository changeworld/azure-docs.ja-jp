---
title: Azure と Dynamics 365 のコンサルティング サービス プラン - プランの設定を定義する |Microsoft Docs
description: Cloud パートナー ポータルで Azure または Dynamics 365 コンサルティング サービス プランの設定を定義するためのガイド。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cb52aea56e9c1183af9ca76201ab65c62a58f22
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972173"
---
# <a name="offer-settings-tab"></a>[プランの設定] タブ

**[新しいプラン]** 画面では、まずプラン ID を作成します。 オファー ID は、次の 3 つのパーツで構成されます。**プラン ID**、**パブリッシャー ID**、**名前**です。 次の各セクションで、それぞれの要素について説明します。

![新しいコンサルティング サービス プランの作成 - [プランの設定] タブ](media/consultingoffer-settings-tab.png)

*[プラン ID]*

この ID は、最初にオファーを送信するときに作成する一意の名前です。 小文字の英数字、ハイフン、アンダースコアのみで構成する必要があります。 **プラン ID** は URL に表示され、検索エンジンの結果に影響します  (例: *yourcompanyname_exampleservice*)。

この例を見るとわかるように、**プラン ID** がパブリッシャー ID に追加されて、一意識別子が作成されます。 これは予約可能な固定リンクとして公開され、検索エンジンによってインデックス付けされます。

"*プランが運用開始になると、その識別子を更新することはできません*"

*[発行元 ID]*

この識別子は、ご自分のアカウントに関連しています。 組織アカウントでログインすると、ドロップダウン メニューに**パブリッシャー** ID が表示されます。

*名前*

この文字列が、AppSource または Azure Marketplace 上で、オファー名として表示されます。 *[名前]* フィールドは 50 文字に制限されています。  プラン名に期間とプランの種類を付加できるだけの余裕を持たせるために、レビュー担当者がタイトルを編集しなければならない場合があります。

Edgewater Fullscope による次の例は、オファー名がどのように組み立てられるかを示しています。 オファー名は次のように表示されます。

![新しいコンサルティング サービス プランの作成](media/cppsampleconsultingoffer.png)

オファー名は、4 つのパーツで構成されます。

-   **期間**: エディターの **[ネットショップの詳細]** タブで定義します。 期間は、時間、日、週で表現できます。
-   **サービスの種類**: エディターの **[ネットショップの詳細]** タブで定義します。 サービスの種類は、`Assessment`、`Briefing`、`Implementation`、`Proof of concept`、`Workshop` です。
-   **前置詞**: レビュー担当者によって挿入されます。
-   **名前**: **[プランの設定]** ページで定義します。

>[!Note]
>[名前] フィールドは 50 文字に制限されています。 提出する名前に期間とプランの種類を付加できるだけの余裕を持たせるために、レビュー担当者が名前を編集しなければならない場合があります。

以下の一覧で適切なオファー名を示します。

-   プロフェッショナル サービスの要点: 1 時間のブリーフィング
-   クラウド移行プラットフォーム: 1 時間のブリーフィング
-   PowerApps および Microsoft Flow: 1 日のワークショップ
-   Azure Machine Learning サービス: 3 週間の PoC
-   ブリック アンド クリック小売りソリューション: 1 時間のブリーフィング
-   データ持ち込み: 1 週間のワークショップ
-   クラウド分析:3 日間のワークショップ
-   Power BI トレーニング: 3 日間のワークショップ
-   セールス管理ソリューション: 1 週間の実装
-   CRM クイック スタート: 1 日のワークショップ
-   Dynamics 365 for Sales: 2 日間の評価

**[プラン設定]** タブの入力が完了したら、送信内容を保存できます。 オファー名はエディターの上に表示されるようになり、[All Offers (すべてのオファー)] に戻るとそれが表示されます。

**次のステップ**

これで[ネットショップの詳細を入力し、Azure Marketplace と AppSource のどちらで発行するかを決定](./cpp-consulting-service-storefront-details.md)することができます。

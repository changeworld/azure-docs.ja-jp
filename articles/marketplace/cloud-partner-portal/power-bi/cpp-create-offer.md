---
title: Power BI アプリケーション プランを作成する - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace 用に Power BI アプリ プランを作成する方法。
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: e0e1311276b858f1ac16fe6f17ccad49dd9901c9
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665933"
---
# <a name="create-a-power-bi-application-offer"></a>Power BI アプリケーション プランを作成する

このセクションでは、[AppSource](https://appsource.microsoft.com) に新しい Power BI アプリ プランを作成するために必要な手順を示します。 各プランは、AppSource で独自のエンティティとして表示されます。  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)で新しいプランを作成するときに、ご利用のプランに対して資産グループを 4 つ指定する必要があります。

|   資産グループ      | 説明                                                                         |
| ----------------   | ----------------                                                                    |
| プラン設定     | プランのプライマリ ID と名前                                      |
| 技術情報     | クライアントの Power BI ワークスペースにアプリをインストールするために使用したインストーラーの URL。 この URL を生成する方法の詳細については、[Power BI アプリに関するドキュメント](https://go.microsoft.com/fwlink/?linkid=2028636)を参照してください。   |
| ネットショップの詳細 | マーケティング、法律、およびリード管理の資産が含まれます。 マーケティング資産には、プランの説明およびロゴが含まれます。  法的資産には、プライバシー ポリシー、使用条件、およびその他の法的文書が含まれます。  リード管理ポリシーにより、AppSource エンド ユーザー ポータルからリードを処理する方法を指定できます。 |
| 連絡先           | サポート連絡先およびポリシー情報が含まれます                                     |
|    |     |


## <a name="new-offer-form"></a>[新しいプラン] フォーム

Cloud パートナー ポータルにサインインしたら、左側のメニュー バーの **[+ 新しいプラン]** 項目をクリックします。  表示されるメニューで **[Power BI アプリ]** をクリックして **[新しいプラン]** フォームを表示し、新しいアプリ プランに資産を定義するプロセスを開始します。

![Power BI プランのメニュー項目](./media/new-offer-menu.png)

> [!WARNING] 
> **[Power BI アプリ]** オプションが表示されない場合、または有効にならない場合は、このプランの種類を作成するためのアクセス許可がアカウントにありません。 開発者アカウントの登録など、このオファーの種類に対するすべての[前提条件](./cpp-prerequisites.md)を満たしていることを確認してください。


## <a name="next-steps"></a>次の手順

このセクションの後続の記事は、**[新しいプラン]** ページ (Power BI アプリ プランの種類用) のタブに対応しています。 各記事では、関連付けられたタブを使用して、新しいアプリ プランに対して資産グループとサポート サービスを定義する方法が説明されています。

-  [[プランの設定] タブ](./cpp-offer-settings-tab.md)
-  [[技術情報] タブ](./cpp-technical-info-tab.md)
-  [ネットショップの詳細タブ](./cpp-storefront-details-tab.md)
-  [連絡先](./cpp-contacts-tab.md)

---
title: Power BI アプリ オファーのプランの設定 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace 用に Power BI アプリ オファーのプランの設定を構成します。
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
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665813"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI アプリの [プランの設定] タブ

サービス アプリに対する **[新しいプラン]** ページの最初のタブは **[プランの設定]** という名前です。  このタブで、オファーのプライマリ ID と名前を指定します。フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![[プランの設定] タブ](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>[プランの設定] のフィールド 

**[プランの設定]** タブでは、次の必須フィールドを指定する必要があります。

|  フィールド        |  説明                                                               |
|---------------|----------------------------------------------------------------------------|
| **[プラン ID]**  | オファーの (発行元プロファイル内での) 一意識別子です。 この ID は、製品 URL、Resource Manager テンプレート、および課金レポートに表示されます。 最大長は 50 文字で、小文字の英数字とハイフン (-) だけを使用でき、ダッシュで終わることはできません。 このフィールドは、オファーの運用開始後に変更することはできません。 たとえば、Contoso がオファー ID `sample-SvcApp` でオファーを発行した場合、そのオファーには AppSource URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp` が割り当てられます。      |
| **発行元** | [AppSource](https://appsource.microsoft.com) での組織の一意識別子です。 すべてのオファリングには、発行元 ID が関連付けられる必要があります。 この値は、オファーを保存した後は変更できません。                         |
| **Name**      | オファーの表示名です。 この名前は、AppSource と Cloud パートナー ポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を含めることをお勧めします。 アプリの販売方法である場合を除き、組織名はここに含めないでください。 他の Web サイトやパブリケーションでこのオファーを販売している場合は、すべてのパブリケーションで名前が同じであることを確認します。    <br/>Power BI アプリ (プレビュー モード) のプレビュー期間中にオファーをリリースする場合、文字列 `(Preview)` をアプリケーションの名前に追加します (たとえば、`Sample Scv App (Preview)`)。 |
|     |     |


## <a name="next-steps"></a>次の手順

次のタブで、オファーの[技術情報](./cpp-technical-info-tab.md)を指定します。

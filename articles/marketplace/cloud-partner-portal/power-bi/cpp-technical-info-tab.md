---
title: Power BI アプリ オファーの技術情報 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace 用に Power BI アプリ オファーに技術情報フィールドを構成する。
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
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744436"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI アプリの [技術的情報] タブ

**[新しいプラン]** ページの **[技術情報]** タブでは、Power BI のインストーラー パッケージ URL と新しいオファーの検証に必要なすべての追加情報を指定します。  初期リリースでは、すべての Power BI アプリは無料で、AppSource から追加料金なしでダウンロードできます。 その結果、このオファーの種類に対しては、Stock Keeping Unit (SKU) が一切定義できなくなります。

![[技術情報] タブ](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>技術情報のフィールド 

**[技術情報]** タブでは、次のフィールドを指定する必要があります。  フィールド ラベルに付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

|        フィールド          |  説明                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installer URL\(インストーラー URL\)**     | アプリを公開し、運用環境に昇格するときに Power BI で生成されるアドレス。  URL の生成方法の詳細については、[Power BI でのサービス アプリの公開](https://docs.microsoft.com/power-bi/service-create-distribute-apps)に関するページを参照してください。  |
|  **Validation instructions\(検証手順\)**  |  Microsoft 検証チームがご使用のアプリの構成、接続、テストを支援するための、省略可能なテキストの手順 (最大 3,000 文字)。これには、一般的な構成設定、"データに接続" オプションなどをテストするために使用できるテスト アカウントやパラメーターなどが含まれます。この情報は、検証チームだけに表示され、検証目的でのみ使用されます。  |
| **Is this app created as a Power BI content pack?\(このアプリは、Power BI コンテンツ パックとして作成されていますか?\)** | 現時点では、これは内部で使用されるフィールドです。 値は既定値 `No` のままにしておきます。このフィールドを `Yes` に変更すると、公開が妨げられる可能性があります。  |  
|  |  |


## <a name="next-steps"></a>次の手順

次の [[電子店舗の詳細]](./cpp-storefront-details-tab.md) タブでは、アプリに関するマーケティングおよび法的な情報を指定します。


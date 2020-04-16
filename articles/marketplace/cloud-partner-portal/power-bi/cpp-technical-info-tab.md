---
title: Power BI アプリ オファーの技術情報 | Azure Marketplace
description: Microsoft AppSource Marketplace 用に Power BI アプリ オファーに技術情報フィールドを構成する。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d814ebc34193f5d7c0c3828d32aa3d2af29f3679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981360"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI アプリの [技術的情報] タブ

>[!Important]
>2020 年 4 月 13 日以降、パートナー センターへの Power BI アプリ オファーの管理の移行を開始します。 移行後は、パートナー センターにてオファーを作成・管理することになります。 [Power BI アプリ作成の概要](https://aka.ms/AzureCreatePBIServiceApp)の手順に従い、移行後のオファーを管理します。

**[新しいオファー]** ページ上の **[技術情報]** タブを使用して、Power BI インストーラー パッケージの URL と新しいオファーを検証するために必要なその他の情報を指定します。  初期リリースでは、すべての Power BI アプリは無料で、AppSource からのダウンロードが可能です。 このため、このオファーの種類に対して Stock Keeping Unit (SKU) を定義することはできません。

![[技術情報] タブ](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>技術情報のフィールド 

**[技術情報]** タブ上で、次の表に示したフィールドを入力します。 フィールド ラベルの末尾のアスタリスク (*) は、そのフィールドが必須であることを意味しています。

|        フィールド          |  説明                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installer URL\(インストーラー URL\)\***     | Power BI では、アプリを発行して運用環境に昇格するときに、この URL を生成します。  詳しくは、「[Power BI でダッシュボードとレポートを含むアプリを発行する](https://docs.microsoft.com/power-bi/service-create-distribute-apps)」をご覧ください。  |
|  **Validation instructions\(検証手順\)**  |  Microsoft 検証チームがアプリを構成、接続、テストできるように、必要に応じて手順 (最大 3,000 文字) を追加します。 [データに接続] オプションをテストするために使用できる一般的な構成設定、アカウント、パラメーター、またはその他の情報を含めます。 この情報は、検証チームだけに表示され、検証目的のみに使用されます。  |
| **Is this app created as a Power BI content pack?\(このアプリは、Power BI コンテンツ パックとして作成されていますか?\)** | 現時点では、このフィールドは内部のみで使用されています。 既定の設定である **[いいえ]** のままにしておきます。 設定を **[はい]** に変更すると、発行プロセスが停止される可能性があります。  |  
|  |  |


## <a name="next-steps"></a>次のステップ

[[電子店舗の詳細]](./cpp-storefront-details-tab.md) タブ上で、アプリに関するマーケティングおよび法的な情報を入力します。


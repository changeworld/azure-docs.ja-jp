---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132558"
---
Functions を使用すると、[Azure portal] から関数アプリに Application Insights 統合を簡単に追加できます。

1. [ポータル][Azure portal] で、ページの上部にある検索バーに「`Function Apps`」と入力し、関数アプリを選択して、ウィンドウの上部にある **[Application Insights が構成されていません]** のバナーを選択します。 このバナーが表示されない場合は、アプリで既に Application Insights が有効になっています。

    ![ポータルから Application Insights を有効にする](media/functions-connect-new-app-insights/enable-application-insights.png)

1. 画像の下の表に指定されている設定を使用して、Application Insights リソースを作成します。

   ![Application Insights リソースの作成](media/functions-connect-new-app-insights/ai-general.png)

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | 一意のアプリ名 | 関数アプリと同じ名前を使用するのが最も簡単です。この名前は、サブスクリプション内で一意である必要があります。 | 
    | **場所** | 西ヨーロッパ | 可能であれば、お使いの関数アプリと同じ[リージョン](https://azure.microsoft.com/regions/)、または近隣のリージョンを使用してください。 |

1. **[OK]** を選択します。 Application Insights リソースは、関数アプリと同じリソース グループおよびサブスクリプションに作成されます。 リソースが作成されたら、Application Insights ウィンドウを閉じます。

1. 対象の関数アプリに戻り、 **[アプリケーション設定]** を選択し、 **[アプリケーション設定]** まで下にスクロールします。 `APPINSIGHTS_INSTRUMENTATIONKEY` という設定が表示された場合は、Azure で実行されている関数アプリに対して Application Insights 統合が有効になっています。

[Azure Portal]: https://portal.azure.com

---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121559"
---
Functions を使用すると、[Azure portal] から関数アプリに Application Insights 統合を簡単に追加できます。

1. [Azure portal][Azure portal] で、"**関数アプリ**" を検索して選択し、対象の関数アプリを選択します。 

1. ウィンドウの上部にある **[Application Insights が構成されていません]** バナーを選択します。 このバナーが表示されない場合は、アプリで既に Application Insights が有効になっています。

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="ポータルから Application Insights を有効にする":::

1. 画像の下の表に指定されている設定を使用して、Application Insights リソースを作成します。

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Application Insights リソースを作成します":::。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | 一意のアプリ名 | 関数アプリと同じ名前を使用するのが最も簡単です。この名前は、サブスクリプション内で一意である必要があります。 | 
    | **場所** | 西ヨーロッパ | 可能であれば、お使いの関数アプリと同じ[リージョン](https://azure.microsoft.com/regions/)、または近隣のリージョンを使用してください。 |

1. **[適用]** を選択します。 Application Insights リソースは、関数アプリと同じリソース グループおよびサブスクリプションに作成されます。 リソースが作成されたら、Application Insights ウィンドウを閉じます。

1. 対象の関数アプリに戻り、 **[設定]**  >  **[構成]** を選択し、 **[アプリケーション設定]** を選択します。 `APPINSIGHTS_INSTRUMENTATIONKEY` という設定が表示された場合は、Azure で実行されている関数アプリに対して Application Insights 統合が有効になっています。

[Azure Portal]: https://portal.azure.com

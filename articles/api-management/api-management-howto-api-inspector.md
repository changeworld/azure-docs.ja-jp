---
title: Azure API Management で要求トレースを使用して API をデバッグする | Microsoft Docs
description: このチュートリアルの手順に従って、Azure API Management で要求処理手順を検査する方法を学びます。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 01a9120af2fb240eed1463613f7a6f946a9c16ed
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934693"
---
# <a name="debug-your-apis-using-request-tracing"></a>要求トレースを使用して API をデバッグする

このチュートリアルでは、API のデバッグとトラブルシューティングに役立つ要求処理の検査方法について説明します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 呼び出しのトレース

![API インスペクター](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
+ また、「[Import and publish your first API (最初の API をインポートして発行する)](import-and-publish.md)」のチュートリアルも完了します。

## <a name="trace-a-call"></a>呼び出しのトレース

1. **[API]** を選択します。
2. API の一覧で **[Demo Conference API]\(デモ会議 API\)** をクリックします。
3. **[GetSpeakers]** 操作を選択します。
4. **[テスト]** タブに切り替えます。
5. **Ocp-Apim-Trace** という名前の HTTP ヘッダーを、値を **true** に設定して含めます。
6. **[送信]** をクリックして、API 呼び出しを行います。 
7. 呼び出しが完了するのを待ちます。 
8. **API コンソール**の **[トレース]** タブに移動します。 詳細なトレース情報に移動するには、リンク (**[受信]**、**[バックエンド]**、**[送信]**) をクリックします。

    **[受信]** セクションには、API Management が呼び出し元から受信した元の要求と、要求に適用されるすべてのポリシー (手順 2. で追加した rate-limit、set-header ポリシーなど) が表示されます。

    **[バックエンド]** セクションには、API Management が API バックエンドに送信した要求と、受信した応答が表示されます。
    
    **[送信]** セクションには、呼び出し元に送り返される前に応答に適用されるすべてのポリシーが表示されます。
 
    > [!TIP]
    > 各ステップには、API Management が要求を受信してからの経過時間も表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 呼び出しのトレース

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [リビジョンの使用](api-management-get-started-revise-api.md)

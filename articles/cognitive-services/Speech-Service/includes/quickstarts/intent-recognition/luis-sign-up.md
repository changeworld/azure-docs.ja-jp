---
title: クイック スタート:音声、意図、エンティティを認識する、Python - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660524"
---
意図認識を実行するには、LUIS プレビュー ポータルを使用して、LUIS アカウントとプロジェクトを作成する必要があります。 このクイックスタートで必要なのは、LUIS サブスクリプションのみです。 音声サービスのサブスクリプションは不要です。

まず、LUIS プレビュー ポータルを使用して LUIS アカウントとアプリを作成する必要があります。 作成した LUIS アプリでは、意図、エンティティ、および発話例を提供する、ホーム オートメーション用のあらかじめ構築されたドメインを使用します。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。これは、Speech SDK を使用して呼び出すことができます。 

LUIS アプリを作成するには、次の手順に従います。 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">クイック スタート:あらかじめ構築されたドメイン アプリの構築に関するページ</a>

完了すると、次の 3 つが必要になります。 

* LUIS キー
* LUIS リージョン
* LUIS アプリ ID

この情報は、[LUIS プレビュー ポータル](https://preview.luis.ai/)の次の場所で確認できます。

1. LUIS プレビュー ポータルで、 **[管理]** を選択し、次に **[Azure リソース]** を選択します。 このページでは、LUIS キーと場所 ("_リージョン_" と呼ばれることもあります) を確認できます。  

   > [!div class="mx-imgBorder"]
   > ![LUIS キーと場所](../../../media/luis/luis-key-region.png)

2. キーと場所を確認したら、アプリ ID が必要になります。 **[アプリケーション設定]** を選択すると、このページにアプリ ID が表示されます。

   > [!div class="mx-imgBorder"]
   > ![LUIS アプリ ID](../../../media/luis/luis-app-id.png)
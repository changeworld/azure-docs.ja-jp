---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: fcf3bf29e2cdf89bdc93c7ebac313e5d9a9c18f0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044015"
---
予測エンドポイントへのアクセスには、エンドポイント キーが必要です。 このクイック スタートでは、LUIS アカウントに関連付けられている無料のスタート キーを使用します。 
 
1. LUIS アカウントを使用してサインインします。 

    [![Language Understanding (LUIS) アプリ リストのスクリーン ショット](media/cognitive-services-luis/app-list.png "Language Understanding (LUIS) アプリ リストのスクリーンショット")](media/cognitive-services-luis/app-list.png)

2. 右上のメニューで名前を選択し、**[Settings]\(設定)** を選択します。

    ![LUIS ユーザー設定メニューへのアクセス](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. **[Authoring key]\(オーサリング キー)** の値をコピーします。 後ほど、このクイック スタートで使用します。 

    [![Language Understanding (LUIS) ユーザー設定のスクリーン ショット](media/cognitive-services-luis/get-user-authoring-key.png "Language Understanding (LUIS) ユーザー設定のスクリーン ショット")](media/cognitive-services-luis/get-user-authoring-key.png)

    オーサリング キーを使うと、すべての LUIS アプリで、オーサリング API への要求は無料で無制限に、予測エンドポイント API へのクエリは 1 か月あたり 1000 回まで行うことができます。 <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
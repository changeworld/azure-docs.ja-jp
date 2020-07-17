---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 4fae62a3ead674eec427474ea38fb399d5c37ff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482277"
---
1. Visual Studio 2019 Community エディションを開きます。
1. 新しい**コンソール アプリ (.NET Core)** プロジェクトを作成し、そのプロジェクトに `QnaMakerQuickstart` という名前を付けます。 残りの設定には既定値を受け入れます。
1. ソリューション エクスプローラーで、プロジェクト名 **QnaMakerQuickstart** を右クリックし、 **[NuGet パッケージの管理...]** を選択します。
1. NuGet ウィンドウで、 **[ブラウザー]** を選択したら、**Newtonsoft.JSON** を検索してパッケージをインストールします。 QnaMaker HTTP 応答から返される JSON を解析するには、このパッケージが使用されます。 

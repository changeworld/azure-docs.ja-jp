---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019092"
---
1. Visual Studio 2017 Community エディションを開きます。
1. 新しい**コンソール アプリ (.NET Core)** プロジェクトを作成し、そのプロジェクトに名前を付けます`QnaMakerQuickstart`。 残りの設定には既定値を受け入れます。
1. ソリューション エクスプローラーで、プロジェクト名 **QnaMakerQuickstart** を右クリックし、**[NuGet パッケージの管理...]** を選択します。
1. NuGet ウィンドウで、**[ブラウザー]** を選択したら、**Newtonsoft.JSON** を検索してパッケージをインストールします。 QnaMaker HTTP 応答から返される JSON を解析するには、このパッケージが使用されます。 
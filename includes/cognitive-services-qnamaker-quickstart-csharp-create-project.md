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
ms.openlocfilehash: 6bda00a20dd83e005a57b50b0183b2b9f49667b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291110"
---
1. Visual Studio 2017 Community エディションを開きます。
1. 新しい**コンソール アプリ (.NET Core)** プロジェクトを作成し、そのプロジェクトに名前を付けます`QnaMakerQuickstart`。 残りの設定には既定値を受け入れます。
1. ソリューション エクスプローラーで、プロジェクト名 **QnaMakerQuickstart** を右クリックし、**[NuGet パッケージの管理...]** を選択します。
1. NuGet ウィンドウで、**[ブラウザー]** を選択したら、**Newtonsoft.JSON** を検索してパッケージをインストールします。 QnaMaker HTTP 応答から返される JSON を解析するには、このパッケージが使用されます。 

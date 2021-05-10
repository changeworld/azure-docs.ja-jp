---
title: 医療コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089727"
---
Text Analytics for Health のパブリック プレビューへのアクセスを要求するには、[Cognitive Services 要求フォーム](https://aka.ms/csgate)に記入して送信します。  このアプリケーションは、コンテナーとホストされた Web API のパブリック プレビューの両方に適用されます。
このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、Azure Cognitive Services チームがそれをレビューして、プライベート コンテナー レジストリにアクセスするための条件を満たしていることを確認します。

> [!IMPORTANT]
> * このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。
> * コンテナーの実行に使用する Text Analytics リソース (課金エンドポイントと apikey) は、承認された Azure サブスクリプション ID で作成されている必要があります。 
> * Microsoft からのアプリケーションの状態に関する更新については、電子メール (受信トレイと迷惑フォルダーの両方) を確認してください。
> * このコンテナーの URL は変更されています。下記の例を参照してください。 このコンテナーは、2021 年 4 月 26 日以降、`containerpreview.azurecr.io` からダウンロードできなくなります。


承認されたら、[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、Microsoft パブリック コンテナー レジストリからこのコンテナー イメージをダウンロードします。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```

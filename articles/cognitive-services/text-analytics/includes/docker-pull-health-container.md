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
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779686"
---
コンテナーへのアクセスを要求するには、[Cognitive Services コンテナー要求フォーム](https://aka.ms/csgate)に記入して送信します。
このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、Azure Cognitive Services チームがそれをレビューして、プライベート コンテナー レジストリにアクセスするための条件を満たしていることを確認します。

> [!IMPORTANT]
> * このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。
> * コンテナーの実行に使用する Azure リソースは、承認された Azure サブスクリプション ID で作成されている必要があります。 
> * Microsoft からのアプリケーションの状態に関する更新については、電子メール (受信トレイと迷惑フォルダーの両方) を確認してください。

オンボードの電子メールで指定されている資格情報と共に docker login コマンドを使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリに接続します。


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、Microsoft のプライベート コンテナー レジストリからこのコンテナー イメージをダウンロードします。

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```

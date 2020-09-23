---
title: 医療コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906047"
---
コンテナーへのアクセスを要求するには、[Cognitive Services コンテナー要求フォーム](https://aka.ms/cognitivegate)に記入して送信します。
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

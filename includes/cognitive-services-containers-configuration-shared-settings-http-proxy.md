---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 664cea26f910fa5b3354e2879a33de50eb13a7f3
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286161"
---
送信要求を行うために HTTP プロキシを構成する必要がある場合は、次の 2 つの引数を使用します。

| EnableAdfsAuthentication | データ型 | 説明 |
|--|--|--|
|HTTP_PROXY|string|使用するプロキシ。例: `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|プロキシで認証されるために必要な資格情報。例: ユーザー名:パスワード。|
|`<proxy-user>`|string|プロキシのユーザー。|
|`proxy-password`|string|プロキシの `<proxy-user>` に関連付けられているパスワード。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```

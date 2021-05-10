---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83594401"
---
#### <a name="local-proxy"></a>ローカル プロキシ

`/api` に対するすべての要求を `http://127.0.0.1:7071/api` で実行中の API エンドポイントにルーティングする、Visual Studio Code の Live Server 拡張機能用のプロキシを構成できます。

1. _.vscode/settings.json_ ファイルを開きます。

1. 次の設定を追加して、Live Server のプロキシを指定します。

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   この構成は、ユーザー設定ファイルに保存する代わりに、プロジェクト設定ファイルに保存することをお勧めします。

   プロジェクト設定を使用することで、Visual Studio Code で開かれている他のすべてのプロジェクトにプロキシが適用されなくなります。

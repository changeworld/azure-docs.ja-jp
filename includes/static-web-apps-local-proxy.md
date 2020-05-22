---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
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

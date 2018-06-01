---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367374"
---
### <a name="run-the-service"></a>サービスを実行する

1. F5 キーを押して (またはターミナル ウィンドウで「`azds up`」と入力して)、サービスを実行します。 新たに選択した `scott` 空間で、サービスが自動的に実行されます。 
1. `azds resource list` をもう一度実行すると、サービスが独自の空間で実行されていることを確認できます。 まず、`mywebapi` のインスタンスが `scott` 空間で実行されていることがわかります (`default` で実行されているバージョンもまだ実行されていますが、リストされていません)。 次に、`webfrontend` のアクセス ポイント URL に、*scott.s.* というプレフィックスが付いています。 この URL は `scott` 空間に固有のもので、"scott URL" に送信された要求が、まず `scott` 空間内のサービスにルーティングされ、その後 `default` 空間内のサービスにフォールバックされることを示します。

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Azure Dev Spaces のこの組み込み機能を使用すれば、共有の環境内でコードをエンド ツー エンドにテストできます。各開発者が、自分の空間内にサービスをフル スタックで再作成する必要はありません。 このルーティングを行うには、このガイドの前の手順で示したように、アプリ コードで伝達ヘッダーを転送する必要があります。

## <a name="test-code-in-a-space"></a>空間内でコードをテストする
`mywebapi` の新しいバージョンと `webfrontend` をテストするには、ブラウザーを開いて、webfrontend のパブリック アクセス ポイント URL に移動します。 すると、新しいメッセージが表示されます。

ここで、URL の "scott.s" の部分を削除し、 ブラウザーをリフレッシュします。 すると、以前の動作が表示されます (`default` で実行されているバージョンの `mywebapi` による動作)
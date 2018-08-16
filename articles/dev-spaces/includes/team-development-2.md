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
ms.openlocfilehash: 8d0de9b027cff375526ef570e88540acfed3ef1c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129111"
---
### <a name="run-the-service"></a>サービスを実行する

1. F5 キーを押して (またはターミナル ウィンドウで「`azds up`」と入力して)、サービスを実行します。 新たに選択した `default/scott` 空間で、サービスが自動的に実行されます。 
1. `azds list-up` をもう一度実行すると、サービスが独自の空間で実行されていることを確認できます。 まず、`mywebapi` のインスタンスが `default/scott` 空間で実行されていることがわかります (`default` で実行されているバージョンもまだ実行されていますが、リストされていません)。 次に、`webfrontend` のアクセス ポイント URL に、"scott.s." というプレフィックスが付いていることがわかります。 この URL は `default/scott` 空間に固有のものです。 この特別な URL は、"scott URL" に送信された要求で、まず、`default/scott` 空間内のサービスへのルーティングが試行され、失敗した場合は、`default` 空間内のサービスにフォールバックされることを示します。

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Azure Dev Spaces のこの組み込み機能を使用すれば、共有空間内でコードをテストできます。その場合、各開発者が、自分の空間内にサービスをフル スタックで再作成する必要はありません。 このルーティングを行うには、このガイドの前の手順で示したように、アプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-in-a-space"></a>空間内でコードをテストする
新しいバージョンの `mywebapi` を `webfrontend` と共にテストするには、ブラウザーで `webfrontend` のパブリック アクセス ポイントの URL を開き、About ページに移動します。 すると、新しいメッセージが表示されます。

ここで、URL の "scott.s" の部分を削除し、 ブラウザーをリフレッシュします。 すると、(`default` で実行されている `mywebapi` バージョンによる) 以前の動作が表示されます

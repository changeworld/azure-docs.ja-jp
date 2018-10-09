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
ms.openlocfilehash: 8580e19f1955becb2cfaee13742054af1a9edb7f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410462"
---
### <a name="run-the-service"></a>サービスを実行する

1. F5 キーを押して (またはターミナル ウィンドウで「`azds up`」と入力して)、サービスを実行します。 新たに選択した `default/scott` 空間で、サービスが自動的に実行されます。 
1. `azds list-up` をもう一度実行すると、サービスが独自の空間で実行されていることを確認できます。 まず、`mywebapi` のインスタンスが `default/scott` 空間で実行されていることがわかります (`default` で実行されているバージョンもまだ実行されていますが、リストされていません)。 `azds list-uris` を実行すると、`webfrontend` のアクセス ポイント URL に "scott.s." というプレフィックスが付きます。 この URL は `default/scott` 空間に固有のものです。 この特別な URL は、"scott URL" に送信された要求で、まず、`default/scott` 空間内のサービスへのルーティングが試行され、失敗した場合は、`default` 空間内のサービスにフォールバックされることを示します。

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Azure Dev Spaces のこの組み込み機能を使用すれば、共有空間内でコードをテストできます。その場合、各開発者が、自分の空間内にサービスをフル スタックで再作成する必要はありません。 このルーティングを行うには、このガイドの前の手順で示したように、アプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-in-a-space"></a>空間内でコードをテストする
新しいバージョンの `mywebapi` を `webfrontend` と共にテストするには、ブラウザーで `webfrontend` のパブリック アクセス ポイントの URL を開き、About ページに移動します。 すると、新しいメッセージが表示されます。

ここで、URL の "scott.s" の部分を削除し、 ブラウザーをリフレッシュします。 (`default` で実行されている `mywebapi` バージョンによる) 以前の動作が表示されます。

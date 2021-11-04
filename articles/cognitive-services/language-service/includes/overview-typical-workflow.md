---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2502698a95671bf2b6ca9960a7f006a12d3a7ce8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017538"
---
## <a name="typical-workflow"></a>一般的なワークフロー

この機能を使用するには、分析のためのデータを送信し、アプリケーションで API 出力を処理します。 分析はそのままの状態で行われ、データに使用されるモデルに対して追加のカスタマイズは行われません。

1. Azure 言語リソースを作成します。これにより、Azure Cognitive Service for Language によって提供される機能にアクセスできるようになります。 API 要求の認証に使用するパスワード (キーと呼ばれる) とエンドポイント URL が生成されます。

2. REST API か、C#、Java、JavaScript、Python のクライアント ライブラリを使用して、要求を作成します。 バッチ要求で非同期呼び出しを送信して、複数の機能の API 要求を 1 回の呼び出しに統合することもできます。

3. 未加工の非構造化テキストのデータを含む要求を送信します。 キーとエンドポイントを使用して認証が行われます。

4. 応答をローカルでストリームまたは保存します。 

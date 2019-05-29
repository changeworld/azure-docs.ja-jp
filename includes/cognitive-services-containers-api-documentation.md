---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124307"
---
## <a name="validate-that-a-container-is-running"></a>コンテナーが実行されていることを検証する 

コンテナーが実行されていることを検証する方法は複数あります。 

|Request|目的|
|--|--|
|`http://localhost:5000/`|コンテナーには、ホーム ページが用意されています。|
|`http://localhost:5000/status`|GET で要求され、エンドポイント クエリを発生させずにコンテナーが実行されていることを検証します。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。|
|`http://localhost:5000/swagger`|コンテナーには、エンドポイントの完全なドキュメント一式と、`Try it now` 機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |

![コンテナーのホーム ページ](./media/cognitive-services-containers-api-documentation/container-webpage.png)

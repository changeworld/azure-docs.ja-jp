---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: ce7d8628c28a4a202a05aeea60e71655b4b7f1a2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984907"
---
Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services のコンテナーから Microsoft に顧客データ (発話) が送信されることはありません。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

`docker run` では、次の引数が課金の目的に使用されます。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Cognitive Service リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みのリソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Cognitive Service リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの LUIS Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | コンテナーのライセンスに同意していることを示します。<br/>このオプションの値は `accept` に設定する必要があります。 |

> [!IMPORTANT]
> 3 つすべてのオプションに有効な値が指定されている必要があります。そうでないと、コンテナーが起動しません。

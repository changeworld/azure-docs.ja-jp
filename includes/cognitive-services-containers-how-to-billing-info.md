---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684652"
---
コンテナーへのクエリは、`<ApiKey>` に使用される Azure リソースの価格レベルで課金されます。

Cognitive Services コンテナーは、計測のために課金エンドポイントに接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に課金エンドポイントに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。 

### <a name="connecting-to-azure"></a>Azure への接続

コンテナーには、実行する課金引数の値が必要です。 これらの値により、コンテナーは課金エンドポイントに接続することができます。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。 許可された時間枠内でコンテナーが Azure に接続しなかった場合、コンテナーは引き続き実行されますが、課金エンドポイントが復元されるまでクエリには対応しません。 接続は、10 ～15 分の同じ時間間隔で、10 回試行されます。 10 回以内に課金エンドポイントに接続できなかった場合は、コンテナーの実行が停止されます。 

### <a name="billing-arguments"></a>課金引数

`docker run` コマンドでコンテナーを起動するには、次の 3 つのオプションすべてに有効な値を指定する必要があります。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Cognitive Service リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みのリソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Cognitive Service リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | コンテナーのライセンスに同意していることを示します。<br/>このオプションの値は `accept` に設定する必要があります。 |



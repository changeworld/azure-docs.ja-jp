---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89411738"
---
## <a name="get-the-training-and-prediction-keys"></a>トレーニング キーと予測キーを取得する

プロジェクトでサービスと対話するには、有効な一連のサブスクリプション キーが必要です。 項目については、[Custom Vision Web サイト](https://customvision.ai)を参照してください。 Custom Vision リソースの作成に使用した Azure アカウントに関連付けられているアカウントでサインインします。 ホーム ページ (新しいプロジェクトを追加するオプションがあるページ) で、右上にある __歯車アイコン__ を選択します。 一覧でトレーニングおよび予測リソースを検索し、展開します。 ここでは、トレーニング キー、予測キー、予測リソース ID の値を見つけることができます。 これらの値を一時的な場所に保存します。

> [!NOTE]
> Cognitive Services のオールインワン キーを利用して Custom Vision にアクセスする場合、設定画面でキーが 1 つだけ表示されます。 この場合、トレーニング操作と予測操作の両方に同じキーを使用します。

![キー UI の画像](../media/csharp-tutorial/training-prediction-keys.png)

または、これらのキーと ID は、[Azure portal](https://www.portal.azure.com) から、Custom Vision トレーニングと予測のリソースを表示し、 __[キー]__ タブに移動して取得することもできます。そこでは、トレーニング キーと予測キーが見つかります。 予測リソースの __[プロパティ]__ タブに移動し、予測リソース ID を取得します。


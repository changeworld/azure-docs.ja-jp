---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: ec6e982f786e1e72ab8743ab26b28f5e331a4e5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725445"
---
`"pageResults"` ノードの下で、キーと値形式の結果ごとに `"confidence"` 値を調べます。 テキスト読み取り操作に対応する `"readResults"` ノード内の信頼度スコアにも注目してください。 読み取り結果の信頼度は、キーと値の抽出結果の信頼度には影響しません。したがって両方を確認します。

* 読み取り操作の信頼度スコアが低い場合は、入力ドキュメントの品質の改善を試みてください (「[入力の要件](../../concept-model-overview.md#input-requirements)」を参照)。

* キーと値の抽出操作の信頼度スコアが低い場合は、分析対象となるドキュメントの種類が、トレーニング セットで使用されているドキュメントと同じであることを確認してください。 トレーニング セットに含まれるドキュメントの体裁にばらつきがある場合は、別々のフォルダーに分けて、バリエーションごとに 1 つのモデルをトレーニングすることを検討してください。

目標とする信頼度スコアは、実際のユース ケースによって異なりますが、一般には、80% 以上のスコアを目標にするのがよいでしょう。 医療記録や請求書の読み取りなど、もっと正確さが要求されるケースでは、100% のスコアが推奨されます。
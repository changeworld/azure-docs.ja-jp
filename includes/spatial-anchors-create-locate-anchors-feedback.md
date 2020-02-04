---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694611"
---
## <a name="provide-feedback-to-the-user"></a>ユーザーへのフィードバックの提供

セッションが更新されるイベントを処理するために、コードを記述できます。 セッションによるユーザーの環境の理解が深まるたびに、このイベントが発生します。 これにより、次のことが可能になります。

- `UserFeedback` クラスを使用して、デバイスが移動し、セッションで環境の把握状況が変わったら、ユーザーにフィードバックを提供する。 これを行うには、次の手順を実行します。
- 空間アンカーを作成するのに十分な追跡済み空間データを確保できるのはどの時点かを判断する。 これは `ReadyForCreateProgress` または `RecommendedForCreateProgress` で判断します。 `ReadyForCreateProgress` が 1 を超えたら、クラウド空間アンカーを保存するのに十分なデータが得られています。ただし、`RecommendedForCreateProgress` が 1 を超えるまで待機することをお勧めします。

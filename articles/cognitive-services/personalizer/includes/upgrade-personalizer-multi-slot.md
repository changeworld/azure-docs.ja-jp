---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: b61e9ba2374286a313444de7ab1e74b17c0a7af5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382275"
---
### <a name="upgrade-personalizer-instance-to-multislot"></a>Personalizer インスタンスをマルチスロットにアップグレードする

Azure portal の **[モデルと学習設定]** ページの Personalizer リソースで、 **[学習設定をエクスポートする]** をクリックします。 ダウンロードした JSON ファイルの **arguments** フィールドは、 **--cb_explore_adf** で始まっています。 これを **--ccb_explore_adf** に変更し、ファイルの残りの部分はそのままにします。 ファイルを保存します。 

![変更前の学習設定](../media/settings/learning-settings-pre-upgrade.png)

![変更後の学習設定](../media/settings/learning-settings-post-upgrade.png)

ポータルの同じタブの **[学習設定のインポート]** で、最近変更した JSON ファイルを参照して選択し、アップロードします。 これにより、Personalizer インスタンスが "マルチスロット" Personalizer に更新され、マルチスロットの Rank と Reward の呼び出しがサポートされるようになります。
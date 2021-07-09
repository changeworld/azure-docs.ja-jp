---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: ce9bc2807708375847594674e5c28330cbe7c23f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110486338"
---
### <a name="upgrade-personalizer-instance-to-multislot"></a>Personalizer インスタンスをマルチスロットにアップグレードする

> [!NOTE]
> マルチスロット パーソナル化 (プレビュー) は、Personalizer サービスの他の機能に影響します。 この変更を元に戻すことはできません。 マルチスロット パーソナル化を有効にする前に、「[マルチスロット パーソナル化 (プレビュー)](../concept-multi-slot-personalization.md)」を参照してください。 

Azure portal の **[モデルと学習設定]** ページの Personalizer リソースで、 **[学習設定をエクスポートする]** をクリックします。 ダウンロードした JSON ファイルの **arguments** フィールドは、 **--cb_explore_adf** で始まっています。 これを **--ccb_explore_adf** に変更し、ファイルの残りの部分はそのままにします。 ファイルを保存します。 

![変更前の学習設定](../media/settings/learning-settings-pre-upgrade.png)

![変更後の学習設定](../media/settings/learning-settings-post-upgrade.png)

ポータルの同じタブの **[学習設定のインポート]** で、最近変更した JSON ファイルを参照して選択し、アップロードします。 これにより、Personalizer インスタンスが "マルチスロット" Personalizer に更新され、マルチスロットの Rank と Reward の呼び出しがサポートされるようになります。

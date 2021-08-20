---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: 0f181a28d1522fb433a274361c96e532c6a0b708
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255370"
---
### <a name="upgrade-personalizer-instance-to-multi-slot"></a>Personalizer インスタンスをマルチスロットにアップグレードする

> [!NOTE]
> マルチスロット パーソナル化 (プレビュー) は、Personalizer サービスの他の機能に影響します。 この変更を元に戻すことはできません。 マルチスロット パーソナル化を有効にする前に、「[マルチスロット パーソナル化 (プレビュー)](../concept-multi-slot-personalization.md)」を参照してください。 


1. Azure portal の **[リソース管理]** の [Personalizer リソース] で自動最適化を無効化し、 **[モデルと学習設定]** ページで自動最適化をオフにして保存します。

> [!NOTE]
> マルチスロット パーソナル化は、自動最適化を無効にしない限り機能しません。 マルチスロット パーソナル化の自動最適化は、今後サポートされる予定です。 

2. Azure portal の **[リソース管理]** の [Personalizer リソース] で Personalizer をマルチスロットに更新し、 **[モデルと学習設定]** ページで、 **[学習設定のエクスポート]** をクリックします。 ダウンロードした JSON ファイルの **arguments** フィールドは、 **--cb_explore_adf** で始まっています。 これを **--ccb_explore_adf** に変更し、ファイルを保存します。 CB (Contextual Bandit) と CCB (条件付き Contextual Bandit) は、それぞれ単一スロットと複数スロットのパーソナル化に使用されるアルゴリズムです。 ADF (Action Dependent Features) は、アクションが機能によって表現または識別されることを意味します。

![変更前の学習設定](../media/settings/learning-settings-pre-upgrade.png)

![変更後の学習設定](../media/settings/learning-settings-post-upgrade.png)

ポータルの同じタブの **[学習設定のインポート]** で、最近変更した JSON ファイルを参照して選択し、アップロードします。 これにより、Personalizer インスタンスが "マルチスロット" Personalizer に更新され、マルチスロットの Rank と Reward の呼び出しがサポートされるようになります。



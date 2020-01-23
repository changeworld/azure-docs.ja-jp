---
title: 学習ポリシーを管理する - Personalizer
description: この記事では、Personalizer についてよく寄せられる質問とその回答を示します。
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75840717"
---
# <a name="manage-learning-policy"></a>学習ポリシーを管理する

学習ポリシーの設定では、モデル トレーニングの "_ハイパーパラメーター_" が決定されます。 学習ポリシーは `.json` ファイルで定義されます。

## <a name="import-a-new-learning-policy"></a>新しい学習ポリシーをインポートする

1. [Azure portal](https://portal.azure.com) を開いて、お使いの Personalizer リソースを選択します。
1. **[リソース管理]** セクションで **[モデルと学習設定]** を選択します。
1. **[学習設定のインポート]** で、前述の JSON 形式で作成したファイルを選択し、 **[アップロード]** ボタンを選択します。

    学習ポリシーが正しくアップロードされたという通知があるまで待機します。

## <a name="export-a-learning-policy"></a>学習ポリシーをエクスポートする

1. [Azure portal](https://portal.azure.com) を開いて、お使いの Personalizer リソースを選択します。
1. **[リソース管理]** セクションで **[モデルと学習設定]** を選択します。
1. **[学習設定のインポート]** で **[学習設定をエクスポートする]** ボタンを選択します。 これにより、`json` ファイルがローカル コンピューターに保存されます。

## <a name="next-steps"></a>次のステップ

学習ポリシーの[概念](concept-active-learning.md#learning-settings)について

[リージョンの可用性の詳細](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
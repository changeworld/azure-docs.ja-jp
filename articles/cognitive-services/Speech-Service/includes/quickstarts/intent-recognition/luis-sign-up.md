---
author: eric-urban
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/04/2021
ms.author: eur
ms.openlocfilehash: d2bda8b0bbd4a4d484d2aa1016bb57244bd6caf7
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507569"
---
意図認識を実行するには、LUIS プレビュー ポータルを使用して、LUIS アカウントとプロジェクトを作成する必要があります。 このクイックスタートでは、[インテント認識が使用可能なリージョンに](../../../regions.md#intent-recognition) LUIS サブスクリプションが必要です。 音声サービスのサブスクリプションは "*不要*" です。

まず、LUIS プレビュー ポータルを使用して LUIS アカウントとアプリを作成する必要があります。 作成した LUIS アプリでは、意図、エンティティ、および発話例を提供する、ホーム オートメーション用のあらかじめ構築されたドメインを使用します。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。これは、Speech SDK を使用して呼び出すことができます。 

LUIS アプリを作成するには、次の手順に従います。

* <a href="/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">クイック スタート: 事前構築済みのドメイン アプリを作成する</a>

完了すると、次の 4 つが必要になります。

* 再発行 (**[Speech priming]\(音声認識の準備\)** をオン)
* LUIS の **プライマリ キー**
* LUIS の **場所**
* LUIS の **アプリ ID**

この情報は、[LUIS プレビュー ポータル](https://preview.luis.ai/)の次の場所で確認できます。

1. LUIS プレビュー ポータルから目的のアプリをアプリし、**[Publish]\(発行\)** ボタンを選択します。

2. **[Production]\(運用\)** スロットを選択します。`en-US` を使用している場合は、 **[change settings]\(設定の変更\)** を選択し、 **[Speech priming]\(音声プライミング\)** オプションを **[On]\(オン\)** の位置に切り替えます。 次に、**[Publish]\(発行\)** ボタンを選択します。

    > [!IMPORTANT]
    > **[Speech priming]\(音声認識の準備\)** の使用を強くお勧めします。音声認識の精度が向上します。

    > [!div class="mx-imgBorder"]
    > ![LUIS をエンドポイントに発行する](../../../media/luis/publish-app-popup.png)

3. LUIS プレビュー ポータルで、**[管理]** を選択し、次に **[Azure リソース]** を選択します。 このページでは、LUIS 予測リソースの LUIS キーと場所 ("_リージョン_" と呼ばれることもあります) を確認できます。

   > [!div class="mx-imgBorder"]
   > ![LUIS キーと場所](../../../media/luis/luis-key-region.png)

4. キーと場所を確認したら、アプリ ID が必要になります。 **[設定]** を選択します。 アプリ ID は、このページから入手できます。

   > [!div class="mx-imgBorder"]
   > ![LUIS アプリ ID](../../../media/luis/luis-app-id.png)

---
title: Azure での LUIS アプリの事前構築済みドメインの使用 | Microsoft Docs
description: Language Understanding Intelligent Service (LUIS) アプリケーションで事前構築済みのドメインを使用する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224179"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>LUIS アプリでの事前構築済みドメインの使用  

Language Understanding (LUIS) には*事前構築済みのドメイン*が用意されています。これは事前構築済みの一連の[意図](luis-how-to-add-intents.md)と[エンティティ](luis-concept-entity-types.md)で、クライアント アプリケーションのドメインまたは共通のカテゴリに対して連携して動作します。 事前構築済みのドメインは事前トレーニング済みで、LUIS アプリに追加する準備ができています。 事前構築済みのドメインの意図とエンティティは、一度アプリに追加したら完全にカスタマイズ可能です。つまり、お使いのシステムの発話でトレーニングできるため、ご自身のユーザーに利用できます。 事前構築済みのドメイン全体をカスタマイズの開始点として使用することも、そのドメインの意図またはエンティティを数個だけ使用することもできます。 

**[Prebuilt domains]\(事前構築済みのドメイン\)** タブを参照すると、ご自身のアプリで使用できる他の事前構築済みのドメインが表示されます。 ドメインのタイルをクリックしてアプリに追加するか、タイルの **[Learn more]\(詳細\)** をクリックして、そのドメインの意図とエンティティを確認します。

> [!TIP]
> [[Prebuilt domains reference]\(事前構築済みのドメインのリファレンス\)](./luis-reference-prebuilt-domains.md) では、事前構築済みのドメインの完全な一覧を確認できます。

![事前構築済みのドメインの追加](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>事前構築済みのドメインの追加
**[Prebuilt domains]\(事前構築済みのドメイン\)** タブで、RestaurantReservation ドメインを検索し、**[ドメインの追加]** をクリックします。 事前構築済みのドメインがご自身の LUIS アプリに追加されたら、**[Intents]\(意図\)** を開いて、RestaurantReservation.Reserve 意図をクリックします。 多くの発話の例が既に用意されていること、また、エンティティでラベル付けされていることがわかります。

![RestaurantReservation.Reserve 意図](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>事前構築済みのドメインからの LUIS アプリの設計
LUIS アプリで事前構築済みのドメインを使用する場合、事前構築済みのドメイン全体をカスタマイズすることも、意図とエンティティを数個だけ使用して開始することもできます。

## <a name="customizing-an-entire-prebuilt-domain"></a>事前構築済みのドメイン全体のカスタマイズ
事前構築済みのドメインは汎用的に設計されています。 多くの意図とエンティティが含まれており、そこから選択して、ニーズに応じてアプリをカスタマイズできます。 事前構築済みのドメイン全体からカスタマイズを開始する場合は、アプリに不要な意図とエンティティを削除します。 事前構築済みのドメインに既に用意されているセットに、意図またはエンティティをいくつか追加することもできます。 たとえば、スポーツ イベント アプリに対して**イベント**事前構築済みドメインを使用する場合は、スポーツ チームのエンティティを追加できます。 LUIS への[発話の提供](luis-how-to-add-example-utterances.md)を開始するときに、ご自身のアプリに固有の用語を追加します。 LUIS はその用語を認識して学習することで、事前構築済みのドメインの意図およびエンティティを、ご自身のアプリのニーズに合わせて調整します。 

> [!TIP]
> 事前構築済みのドメインの意図とエンティティは、組み合わせて使ったときに最も効果的に機能します。 意図とエンティティは、できるだけ同じドメインのものを組み合わせることをお勧めします。
> * ベスト プラクティスは、同じドメインの関連する意図を使用することです。 たとえば、**場所**ドメインの `MakeReservation` 意図をカスタマイズしている場合は、**イベント** ドメインまたは**ユーティリティ** ドメインのキャンセル意図ではなく、**場所**ドメインの `Cancel` 意図を選択します。

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>事前構築済みドメインの意図の動作変更
事前構築済みのドメインに含まれる意図の動作を変えて、LUIS アプリで使用したい場合があります。 たとえば、**場所**事前構築済みドメインに用意されている、レストランを予約するための `MakeReservation` 意図を、ご自身のアプリでは、ホテルを予約をする目的で使いたいと考えています。 この場合、その意図の動作を変更するには、ホテル予約に関する発話を LUIS に提供し、`MakeReservation` 意図を使用してその発話にラベルを付けます。これにより、ホテル予約の要求で `MakeReservation` 意図が認識されるように LUIS を再トレーニングできます。

> [!TIP]
> 任意のドメインで使用できるように、カスタマイズ可能な事前構築済みの意図のユーティリティ ドメインをチェックアウトします。 たとえば、`Utilities.Repeat` をご自身のアプリに追加して、そのアプリケーションでユーザーが繰り返す必要がある任意のアクションが認識されるように、アプリをトレーニングできます。


## <a name="next-step"></a>次のステップ

発話の例をさらに追加して、事前構築済みのドメインをカスタマイズします。

> [!div class="nextstepaction"]
> [発話の例を追加する](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>その他のリソース

事前構築済みのドメインの詳細については、「[Prebuilt domains reference (事前構築済みのドメインのリファレンス)](./luis-reference-prebuilt-domains.md)」を参照してください。

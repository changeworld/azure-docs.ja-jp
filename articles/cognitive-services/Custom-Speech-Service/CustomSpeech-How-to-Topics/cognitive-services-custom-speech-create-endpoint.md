---
title: Azure 上で Custom Speech Service を使用してカスタム音声エンドポイントを作成する | Microsoft Docs
description: Cognitive Services の Custom Speech Service を使用してカスタム Speech-to-Text エンドポイントを作成する方法を説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 99bc275db1f0c1b45b3db440d2e03d0db9ab5cf6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373072"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>カスタム Speech-to-Text エンドポイントの作成
カスタム音響モデルまたは言語モデルを作成したら、カスタム Speech-to-Text エンドポイントに展開できます。 

## <a name="create-an-endpoint"></a>エンドポイントの作成
新しいカスタム エンドポイントを作成するには、ページの一番上の **[Custom Speech]** メニューで **[デプロイ]** を選択します。 この操作により、現在のカスタム エンドポイントの表を含む **[デプロイ]** ページが表示されます。 まだエンドポイントを作成していない場合には表は空です。 現在のロケールが表のタイトルに反映されます。 

別の言語のデプロイを作成するには、**[Change Locale]\(ロケールの変更\)** を選択します。 サポートされる言語について詳しくは、「[Supported locales in Custom Speech Service](cognitive-services-custom-speech-change-locale.md)」(Custom Speech Service でサポートされるロケール) をご覧ください。

新しいエンドポイントを作成するには、**[新規作成]** を選択します。 **[デプロイの作成]** ウィンドウで、カスタム デプロイメントの **[名前]** ボックスと **[説明]** ボックスに情報を入力します。

**[サブスクリプション]** コンボ ボックスで、使用するサブスクリプションを選択します。 S2 サブスクリプションの場合は、スケール ユニットとコンテンツ ログを選択できます。 スケール ユニットおよびログについて詳しくは、「[Custom Speech Service meters and quotas](../cognitive-services-custom-speech-meters.md)」(Custom Speech Service のメーターとクォータ) をご覧ください。

次の表は、スケール ユニットと対応可能な同時要求数がどのようにマップされるかを示しています。

| スケール ユニット | 同時要求数 |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

コンテンツのログのオンまたはオフを選択することもできます。 つまり、マイクロソフト内部で使用するためにエンドポイント トラフィックを格納するかどうかを選択します。 選択されていない場合、トラフィックの格納は抑制されます。 コンテンツのログを抑制すると追加コストが発生します。 詳しくは、[価格情報に関するページ](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)をご覧ください。

> [!NOTE]
> コンテンツのログは、価格のページでは「トレースなし」と示されます。
>


また、大まかなコストの見積もりが示されるため、スケール ユニットとコンテンツ ログのコストの影響を確認できます。 この見積もりは大まかな推定値であり、実際のコストと異なる場合があります。

> [!NOTE]
> これらの設定は F0 (無料の価格レベル) サブスクリプションにはありません。
>

**[Acoustic Model]\(音響モデル\)** リストで必要な音響モデルを選択し、**[Language Model]** リストで必要な言語モデルを選択します。 音響モデルと言語モデルの選択肢には、必ず基本の Microsoft モデルが含まれます。 基本モデルを選択すると組み合わせが制限されます。 会話の基本モデルを検索とディクテーションの基本モデルと混在させることはできません。

![[デプロイの作成] ページ](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> チェック ボックスをオンにして、使用条件と価格情報に同意したことを確認します。
>

音響モデルと言語モデルを選択したら、**[作成]** を選択します。 この操作によって、**[デプロイ]** ページに戻ります。 表に、新しいエンドポイントに対応するエントリが含まれています。 エンドポイントが作成中の場合、状態は現在の状態を反映します。 新しいエンドポイントをカスタム モデルでインスタンス化するには、最大で 30 分かかることがあります。 デプロイの状態が *[完了]* の場合、エンドポイントは使用可能です。

![[デプロイ] ページ](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

デプロイの準備ができたら、デプロイ名がリンクとして表示されます。 リンクを選択すると、**[展開情報]** ページが表示され、HTTP 要求または Microsoft Cognitive Services Speech クライアント ライブラリ (Web ソケット使用) で使用するカスタム エンドポイントの URL が表示されます。

![[展開情報] ページ](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>次の手順

その他のチュートリアルについては、以下を参照してください。
* [カスタム Speech-to-Text エンドポイントの使用](cognitive-services-custom-speech-use-endpoint.md)
* [カスタム音響モデルを作成する](cognitive-services-custom-speech-create-acoustic-model.md)
* [カスタム言語モデルを作成する](cognitive-services-custom-speech-create-language-model.md)

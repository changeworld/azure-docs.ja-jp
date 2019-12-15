---
title: Speech Service を無料で試す
titleSuffix: Azure Cognitive Services
description: 音声サービスは、低コストで簡単に使い始めることができます。 無料で利用できるオプションは 2 つあります。そのため、サービスを使って実行できることを確認し、自分のニーズに適しているかどうかを判断することができます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 3ddba414ef1801b812d157ad734847099a8a9f25
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806186"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service を無料で試す

音声サービスは、低コストで簡単に使うことができます。 無料で利用できるオプションは 2 つあります。そのため、サービスを使って実行できることを確認し、自分のニーズに適しているかどうかを判断することができます。

- クレジット カード情報を指定せずに無料試用版を入手する (既存の Azure アカウントを持っている必要があります)
- 試用期間中に新しい Azure アカウントを無料で作成する (クレジット カード情報が必要)

この記事では、これらのオプションから自分のニーズに最適なものを選択します。

> [!NOTE]
> 音声サービスには、無料とサブスクリプションの 2 つのサービス レベルがあります。これには、異なる制限事項と特典があります。 無料の Azure アカウントにサインアップすると、最大 30 日間有効な、有料の音声サービス サブスクリプションに充当できる 200 ドルのサービス クレジットが付属しています。
>
> 無料の小規模の音声サービス レベルを使用する場合は、無料試用版またはサービス クレジットの有効期限が切れた後でも、この無料のサブスクリプションを保持できます。
>
> 詳細については、「[Cognitive Services の価格 - Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)」を参照してください。

## <a name="try-the-speech-service-without-credit-card-info"></a>クレジット カード情報を使用せずに音声サービスを試す

次のセクションの手順に従って音声サービスを試すことをお勧めしますが、次のような場合には、このセクションの指示に従うことをお勧めします。

- アクティブな Azure アカウントを既に所有している。
- 新しい Azure アカウントを作成せずに音声サービスを評価する必要がある。
- クレジット カードが必要なく、評価期間後にデータを保存しないことを選ぶ。

> [!NOTE]
> 試用期間は、次の手順が完了するとすぐに開始されます。

1. 「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/)」に移動します。
1. **[Speech API]** タブを選択します。
1. **[API キーの取得]** を選択します。

課金の選択肢が表示されます。 無料オプションを選択し、使用許諾を読んで承認します。 期間限定で音声サービスを試すために使用できるキーが表示されます。

## <a name="try-the-speech-service-using-a-new-azure-account"></a>新しい Azure アカウントを使用して音声サービスを試す

新しい Azure アカウントにサインアップするには、Microsoft アカウントが必要です。 Microsoft アカウントがない場合は、[Microsoft アカウント ポータル](https://account.microsoft.com/account)で無料のものにサインアップすることができます。 **[Microsoft アカウントでサインイン]** を選択し、サインインを求められたら **[Microsoft アカウントの作成]** を選択します。 新しい Microsoft アカウントを作成して確認する手順に従います。

Azure アカウントを作成したら、[Azure サインアップ ページ](https://azure.microsoft.com/free/ai/)に移動し、 **[無料で始める]** を選択し、Microsoft アカウントを使用して新しい Azure アカウントを作成します。

### <a name="create-a-speech-resource-in-azure"></a>Azure で Speech リソースを作成する

> [!NOTE]
> Standard レベルのサブスクリプションは、1 つまたは複数のリージョンにいくつでも作成できます。 ただし、Free レベルのサブスクリプションは 1 つしか作成できません。 7 日間使用されなかった無料レベルのモデル デプロイは、自動的に破棄されます。

Speech Service リソース (無料または有料レベル) を Azure アカウントに追加するには:

1. お使いの Microsoft アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. ポータルの左上にある **[Create a resource]\(リソースの作成\)** を選択します。 **[リソースの作成]** が表示されない場合は、左上の折りたたまれたメニューを選択することで、いつでも見つけることができます。

   ![折りたたまれたナビゲーション ボタン](media/index/collapsed-nav.png)

1. **新規**ウィンドウで、検索ボックスに「speech」と入力し、Enter キーを押します。

1. 検索結果で、 **[Speech]** を選択します。

   ![speech の検索結果](media/index/speech-search.png)

1. **[作成]** を選択して、次のことを行います。

   - 新しいリソースに一意の名前を指定します。 この名前は、同じサービスの複数のサブスクリプションを区別するのに役立ちます。
   - 新しいリソースが関連付けられている Azure サブスクリプションを選択して、料金の課金方法を決定します。
   - リソースが使用される[リージョン](regions.md)を選択します。
   - 無料 (F0) または有料 (S0) の価格レベルのどちらかを選択します。 各レベルの価格と使用量クォータの完全な情報については、 **[価格の詳細を表示]** を選択します。
   - この Speech サブスクリプションの新しいリソース グループを作成するか、既存のリソース グループにサブスクリプションを割り当てます。 リソース グループは、さまざまな Azure サブスクリプションを整理しておくのに役立ちます。
   - **作成** を選択します。 これでデプロイの概要に移動し、デプロイの進行状況を示すメッセージが表示されます。

新しい音声リソースを展開するまでに少し時間がかかります。 デプロイが完了したら、 **[リソースに移動]** を選択し、左側のナビゲーション ウィンドウで **[キー]** を選択して、音声サービスのサブスクリプション キーを表示します。 各サブスクリプションには 2 つのキーがあります。アプリケーションでどちらのキーを使用しても構いません。 キーをコード エディターやその他の場所にすばやくコピーして貼り付けるには、各キーの横にあるコピー ボタンを選択し、ウィンドウを切り替えてクリップボードの内容を目的の場所に貼り付けます。

> [!IMPORTANT]
> これらのサブスクリプション キーは、Cognitive Service API にアクセスするために使用されます。 キーを共有しないでください。 Azure Key Vault を使用するなどして、安全に保管してください。 これらのキーを定期的に再生成することもお勧めします。 API 呼び出しを行うために必要なキーは 1 つだけです。 最初のキーを再生成するときに、2 番目のキーを使用してサービスに継続的にアクセスすることができます。

## <a name="switch-to-a-new-subscription"></a>新しいサブスクリプションに切り替える

無料試用版の期限が切れたときやアプリケーションを発行するときにサブスクリプションを切り替えるには、コード内のリージョンとサブスクリプション キーを新しい Azure リソースのリージョンとサブスクリプション キーに置き換えます。

## <a name="about-regions"></a>リージョンについて

- アプリケーションで [Speech SDK](speech-sdk.md) を使用する場合、Speech の構成を作成するときに `westus` などのリージョン コードを指定します。
- アプリケーションで Speech Service の [REST API](rest-apis.md) のいずれかを使用する場合、要求を行うときに使用するエンドポイント URI にリージョンが含まれます。
- あるリージョン用に作成されたキーはそのリージョンでのみ有効です。 別のリージョンで使用すると認証エラーが発生します。

## <a name="next-steps"></a>次の手順

10 分間のクイック スタートのいずれかを完了するか、SDK のサンプルを確認します:

> [!div class="nextstepaction"]
> [クイック スタート: C# で音声を認識する](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Speech SDK サンプル](speech-sdk.md#get-the-samples)

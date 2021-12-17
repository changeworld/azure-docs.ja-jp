---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: adbcef246e8b027230ec436de9d9a1da838e523f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091790"
---
Language Studio を使うには、認証用の言語として Azure リソースが必要です。 このリソースを使って、機能の REST API とクライアント ライブラリを呼び出すこともできます。 作業を開始するには、次の手順に従います。 

> [!IMPORTANT] 
> カスタム機能のセットアップ処理と要件は異なります。 カスタム機能を使っている場合は、以下にリンクされているいずれかのクイックスタートの記事に従うことをお勧めします。そうすることで、より簡単に開始できます。  
> * [Conversational Language Understanding](../conversational-language-understanding/quickstart.md)
> * [カスタム テキスト分類](../custom-classification/quickstart.md)
> * [カスタム固有表現認識 (NER)](../custom-named-entity-recognition/quickstart.md) 

1. Azure サブスクリプションを作成します。 [無料で作成できます](https://azure.microsoft.com/free/ai/)。 

2. [Language Studio にログインします](https://aka.ms/languageStudio)。 初めてログインするときは、言語リソースを選べるウィンドウが表示されます。 

   :::image type="content" source="../media/language-resource-small.png" alt-text="Language Studio のリソースの選択画面を示すスクリーンショット。" lightbox="../media/language-resource.png":::

3. **[Create a new language resource]\(新しい言語リソースを作成する\)** を選びます。 次に、名前、場所、リソース グループなど、新しいリソースの情報を入力します。

    
    > [!TIP]
    > * Azure リソースの場所を選ぶときは、待機時間を短くするために最も近いものを選びます。
    > * Azure 全体で要求を認証するために、 **[マネージド ID]** オプションを **[オン]** にすることをお勧めします。
    > * Free 価格レベルを使う場合は、Azure 無料試用版またはサービス クレジットの有効期限が切れた後でも、言語サービスの使用を継続できます。 

    :::image type="content" source="../media/create-new-resource-small.png" alt-text="Language Studio のリソース作成画面のスクリーンショット。" lightbox="../media/create-new-resource.png":::

4. **[Done]** を選択します。 リソースが作成され、言語サービスによって提供されるさまざまな機能を試すことができます。 たとえば、 **[Find linked entities]\(リンクされたエンティティを検索する\)** を選べます。

    :::image type="content" source="../media/language-studio-main-screen.png" alt-text="Language Studio のメイン画面を示すスクリーンショット。" lightbox="../media/language-studio-main-screen.png":::


5. この機能には、テキストの入力、ファイルのアップロードや、テキスト サンプルを選んで機能の動作方法を示すセクションがあります。 デモを試すには、リソースを選び、[価格レベル](https://aka.ms/unifiedLanguagePricing)に従って使用量が発生することを確認する必要があります。

    :::image type="content" source="../media/language-studio-feature.png" alt-text="Language Studio の機能を示すスクリーンショット" lightbox="../media/language-studio-feature.png":::

6. テキストを送信すると、JSON 応答と共に視覚化を表示できるようになります。 ページの下部に、次の手順と、先ほど送信した API 要求の cURL コマンドが表示されます。

    :::image type="content" source="../media/language-studio-feature-result.png" alt-text="Language Studio の機能の結果を示すスクリーンショット" lightbox="../media/language-studio-feature-result.png":::

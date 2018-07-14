---
title: 最初の Bing Custom Search インスタンスの作成 - Microsoft Cognitive Services
description: Bing Custom Search を使用するには、Web のビューまたはスライスを定義するカスタム検索のインスタンスを作成する必要があります。 インスタンスには、Bing で検索するパブリック ドメイン、サブサイト、および Web ページを定義する設定と、ランク付けの調整が含まれています。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374741"
---
# <a name="create-your-first-bing-custom-search-instance"></a>最初の Bing Custom Search インスタンスの作成
Bing Custom Search を使用するには、Web のビューまたはスライスを定義するカスタム検索のインスタンスを作成する必要があります。 インスタンスには、Bing で検索するパブリック ドメイン、Web サイト、および Web ページを定義する設定と、ランク付けの調整が含まれています。 インスタンスを作成するには、Bing Custom Search の[ポータル](https://customsearch.ai)を使用します。 

## <a name="create-a-custom-search-instance"></a>カスタム検索インスタンスの作成

Bing Custom Search インスタンスを作成するには:

1.  Custom Search API のキーを取得します。 「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)」を参照してください。
2.  Microsoft アカウント (MSA) を使用して、ポータルにサインインします。 **[サインイン]** ボタンをクリックします。 初めてポータルを使用する場合は、以下の追加の手順を実行します。そうでない場合は、手順 3 に進みます。
    - MSA を持っていない場合は、**[Microsoft アカウントの作成]** をクリックします。 ポータルでは、データにアクセスするための権限が求められます。 **[はい]** をクリックします。
    - Cognitive Services 使用条件に同意します。 **[I agree]\(同意する\)** のチェックをオンにして、**[ 同意する]** をクリックします。  
3.  サインイン後、**[新しいインスタンス]** をクリックして、インスタンスに名前を付けます。 意味がわかる名前を使用して、検索によって返されるコンテンツの種類を説明します。 名前はいつでも変更できます。 
4.  **[検索エクスペリエンス]** で **[アクティブ]** タブをクリックし、検索に含める 1 つまたは複数のサイトの URL を入力します。
5.  インスタンスが結果を返すことを確認するには、右側のプレビュー ウィンドウにクエリを入力します。 結果がない場合は、新しいサイトを指定します。 Bing は、インデックスが付けられている公開サイトの結果のみを返します。
6.  構成の変更を実稼動環境に公開するために、**[公開]** をクリックします。 メッセージが表示されたら、**[公開]** をクリックして確定します。
7.  **[実稼動]** > **[エンドポイント]** の順にクリックして、**[Custom Configuration ID]\(カスタム構成 ID\)** をコピーします。 Custom Search API を呼び出すために、この ID が必要になります。

## <a name="next-steps"></a>次の手順

この記事で作成したカスタム検索のインスタンスを引き続き操作するには、操作方法に関する以下のガイドの手順に従います。

- [カスタム検索エクスペリエンスの構成](./define-your-custom-view.md)
- [カスタム検索の呼び出し](./search-your-custom-view.md)
- [カスタム検索の共有](./share-your-custom-search.md)
- [ホストされている UI エクスペリエンスの構成](./hosted-ui.md)
- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)

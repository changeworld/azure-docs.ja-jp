---
title: Azure API Management で成果物を作成して発行する方法
description: Azure API Management で成果物を作成して発行する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: af1768a6555168b777e68f378d32a0b44e9b2c78
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-publish-a-product"></a>製品を作成して発行する  

Azure API Management の成果物には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。 成果物が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。  

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 製品を作成して発行する
> * 製品に API を追加する

![追加された製品](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
+ また、「[Import and publish your first API (最初の API をインポートして発行する)](import-and-publish.md)」のチュートリアルも完了します。

## <a name="create-and-publish-a-product"></a>製品を作成して発行する

1. 左側のメニューの **[製品]** をクリックして **[製品]** ページを表示します。
2. **[+ Product]\(+ 製品\)** をクリックします。

    ![追加された製品](media/api-management-howto-add-products/add-product.png)

    製品を追加するときに、次の情報を指定する必要があります。 

    |Name|[説明]|
    |---|---|
    |表示名|**[開発者ポータル]** で表示する名前です。|
    |Name|製品のわかりやすい名前。|
    |[説明]|**[説明]** フィールドには、その目的、アクセスできる API、その他の有益な情報など、製品に関する詳しい情報を入力できます。|
    |State|製品を発行する場合は **[発行済み]** を押します。 成果物に含まれる API を呼び出すには、あらかじめ成果物を発行しておく必要があります。 新しい成果物は既定で発行されず、**Administrators** グループのみに表示されます。|
    |承認が必要|この成果物に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合は、 **[サブスクリプションの承認を必須とする]** チェック ボックスをオンにします。 チェック ボックスがオフの場合、サブスクリプションの申し込みは自動承認されます。 |
    |サブスクリプション数の制限|複数同時に利用できるサブスクリプションの数を制限するには、サブスクリプションの上限を入力します。 |
    |法的条項|サブスクライバーが製品を使用するにあたって同意する必要がある製品の使用条件を入力できます。|
    |API|製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 <br/> 製品の作成時に既存の API を追加できます。 製品には後から API を追加できます。**[設定]** ページを使用して追加することも、API の作成時に追加することもできます。|<br/>開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。<br/> APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。|

3. **[作成]** をクリックして新しい製品を作成します。

### <a name="add-more-configurations"></a>構成をさらに追加する

**[設定]** タブを選択すると、保存した後で続けて製品を構成できます。 

**[サブスクリプション]** タブでサブスクライバーを表示するか、または製品にサブスクライバーを追加します。

**[アクセス制御]** タブで開発者やゲスト向けに製品の可視性を設定します。

## <a name="add-apis"> </a>成果物への API の追加

製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 製品の作成時に既存の API を追加できます。 製品には後から API を追加できます。**[設定]** ページを使用して追加することも、API の作成時に追加することもできます。

開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

### <a name="add-an-api-to-an-existing-product"></a>既存の製品に API を追加する

1. 製品を選択します。
2. [API] タブを選択します。
3. **[+ API]** をクリックします。
4. API を選択して **[作成]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 製品を作成して発行する
> * 製品に API を追加する

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [空の API とAPI のモック応答を作成する](mock-api-responses.md)

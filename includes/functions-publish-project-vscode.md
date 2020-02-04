---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842193"
---
## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。 

1. アクティビティ バーで Azure アイコンを選択し、 **[Azure: Functions]** 領域の **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Prompt | Value | [説明] |
    | ------ | ----- | ----- |
    | サブスクリプションの選択 | 該当するサブスクリプション | 複数のサブスクリプションがある場合に表示されます。 |
    | Select function app in Azure (Azure で関数アプリを選択してください) | + Create new Function App (新しい Function App を作成します) | 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。 |
    | 関数アプリのグローバルに一意の名前を入力します。 | 一意の名前 | 関数アプリ名の有効な文字は、`a-z`、`0-9`、`-` です。 |
    | Select a location for new resources (新しいリソースの場所を選択する) | リージョン | 近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Prompt | Value | [説明] |
    | ------ | ----- | ----- |
    | サブスクリプションの選択 | 該当するサブスクリプション | 複数のサブスクリプションがある場合に表示されます。 |
    | Select function app in Azure (Azure で関数アプリを選択してください) | + Create new Function App (新しい Function App を作成します) | 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。 |
    | 関数アプリのグローバルに一意の名前を入力します。 | 一意の名前 | 関数アプリ名の有効な文字は、`a-z`、`0-9`、`-` です。 |
    | Select a runtime (ランタイムを選択してください) | バージョン | ローカルで実行している言語バージョンを選択してください。 |
    | Select a location for new resources (新しいリソースの場所を選択する) | リージョン | 近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 | 

    ::: zone-end

    
1.  完了すると、次の Azure リソースがサブスクリプションに作成されます。

    + **[リソース グループ](../articles/azure-resource-manager/management/overview.md)** :作成された Azure リソースがすべて含まれます。 名前は関数アプリの名前に基づきます。
    + **[ストレージ アカウント](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** :関数アプリの名前に基づいた一意の名前で、Standard Storage アカウントが作成されます。
    + **[ホスティング プラン](../articles/azure-functions/functions-scale.md)** :サーバーレス関数アプリをホストするために、従量課金プランが米国西部リージョンに作成されます。
    + **関数アプリ**:プロジェクトはこの新しい関数アプリにデプロイされ、そこで実行されます。
    + **[Application Insights]()** : 関数アプリに関連付けられたインスタンスが関数名に基づいて作成されます。

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 
    
1. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。

    ![作成完了通知](media/functions-publish-project-vscode/function-create-notifications.png)

1. **[Azure: Functions]** 領域 (サイド バー内) に戻り、サブスクリプションの下にある新しい関数アプリを展開します。 **[Functions]** を展開し、 **[HttpExample]** を右クリック (Windows) または Ctrl キーを押しながらクリック (MacOS) して、 **[Copy function URL]\(関数 URL のコピー\)** を選択します。

    ![新しい HTTP トリガーの関数 URL をコピーします](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)

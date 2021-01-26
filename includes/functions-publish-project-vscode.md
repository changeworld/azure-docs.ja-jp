---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a525d1e14e642a64235c263ba29bf7a181bf9e30
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008200"
---
## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。

> [!IMPORTANT]
> 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。


1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    - **Select folder (フォルダーを選択してください)** : ワークスペースのフォルダーを選択するか、関数アプリが格納されているフォルダーを参照します。 既に有効な関数アプリが開いている場合には、このプロンプトは表示されません。

    - **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみの場合、このプロンプトは表示されません。

    - **Select function app in Azure (Azure で関数アプリを選択してください)** : [`- Create new Function App`] を選択します。 (`Advanced` オプションはこの記事では取り上げません。選択しないでください。)
      
    - **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : URL パスに有効な名前を入力します。 入力した名前は、Azure Functions 内での一意性を確保するために検証されます。
    
    - **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。 
    
1.  完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。
    
    - リソース グループ。関連リソースの論理コンテナーです。
    - Standard Azure ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
    - 従量課金プラン。サーバーレス関数アプリの実行環境となるホストを定義します。 
    - 関数アプリ。関数コードを実行するための環境となります。 関数アプリを使用すると、同じホスティング プランに含まれるリソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。
    - 関数アプリに接続された Application Insights インスタンス。サーバーレス関数の使用を追跡します。

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 
    
1. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。 通知を見逃した場合は、右下隅にあるベル アイコンを選択して、再度確認します。

    ![作成完了通知](media/functions-publish-project-vscode/function-create-notifications.png)

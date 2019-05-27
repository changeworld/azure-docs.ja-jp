---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: 79dbee33928fbc7560d0ea27be3af25cc510e996
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132282"
---
## <a name="create-an-azure-functions-project"></a>Azure Functions プロジェクトを作成する

Visual Studio Code の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースを管理、デプロイ、および共有するための論理ユニットとして関数をグループ化できます。

1. Visual Studio Code で、Azure ロゴを選択して **[Azure:Functions]** 領域を表示し、[新しいプロジェクトの作成] アイコンを選択します。

    ![関数アプリ プロジェクトを作成する](./media/functions-create-function-app-vs-code/create-function-app-project.png)

1. プロジェクト ワークスペースの場所を選択し、**[選択]** をクリックします。

    > [!NOTE]
    > この記事は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. 関数アプリ プロジェクトの言語を選択します。 この記事では、JavaScript を使用しています。
    ![プロジェクトの言語を選択する](./media/functions-create-function-app-vs-code/create-function-app-project-language.png)

1. メッセージが表示されたら、**[Add to workspace]\(ワークスペースに追加\)** を選択します。

Visual Studio Code によって、新しいワークスペースに関数アプリ プロジェクトが作成されます。 このプロジェクトには、[host.json](../articles/azure-functions/functions-host-json.md) および [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) 構成ファイルと、言語固有のプロジェクト ファイルが含まれています。 プロジェクト フォルダーに新しい Git リポジトリも取得します。
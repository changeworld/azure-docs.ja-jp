---
title: Visual Studio でサーバーレス アプリを構築する | Microsoft Docs
description: Visual Studio で Azure Logic Apps と Azure Functions を使用して初めてのサーバーレス アプリを構築、展開、管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.custom: vs-azure
ms.topic: article
ms.date: 08/01/2018
ms.openlocfilehash: a69c129d5ae1405462e3a54a24cd2edbad2a86a7
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126779"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Azure Logic Apps と Azure Functions を使用して初めてのサーバーレス アプリを構築する - Visual Studio

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) や [Azure Functions](../azure-functions/functions-overview.md) などの Azure のサーバーレス ツールと機能を使用すると、クラウド アプリを短時間で開発して展開できます。 この記事では、Visual Studio で、Azure 関数を呼び出すロジック アプリを使用するサーバーレス アプリの構築を始める方法を示します。 Azure でのサーバーレス ソリューションについて詳しくは、「[Functions と Logic Apps を含む Azure Serverless の概要](../logic-apps/logic-apps-serverless-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Visual Studio でサーバーレス アプリを構築するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [Visual Studio 2017](https://www.visualstudio.com/vs/) または Visual Studio 2015 - Community、Professional、または Enterprise

* [Microsoft Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以降)

* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

* [Azure Logic Apps Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) または [Visual Studio 2015 バージョン](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)

  Azure Logic Apps Tools は、Visual Studio Marketplace から直接ダウンロードしてインストールできます。または、[この拡張機能を Visual Studio 内からインストールする方法](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)を確認できます。 
  インストールが完了したら、必ず Visual Studio を再起動してください。 

* ローカルで関数をデバッグするための [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)

* Visual Studio に組み込まれたロジック アプリ デザイナーを使用するときの Web へのアクセス

  デザイナーが Azure でリソースを作成し、ロジック アプリでコネクタからプロパティとデータを読み取るためには、インターネット接続が必要です。 
  たとえば、Dynamics CRM Online コネクタを使用する場合、デザイナーは CRM インスタンスの使用可能な既定およびカスタムのプロパティをチェックします。

## <a name="create-resource-group-project"></a>リソース グループ プロジェクトを作成する

最初に、サーバーレス アプリ用の [Azure リソース グループ プロジェクト](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を作成します。 Azure では、リソース グループ内にリソースを作成します。リソース グループは、アプリ全体に対するリソースを 1 つの資産として整理、管理、展開するために使用する論理コレクションです。 Azure のサーバーレス アプリの場合、リソース グループには Azure Logic Apps と Azure Functions の両方に対するリソースが含まれています。 Azure リソース グループとリソースについて詳しくは、[こちら](../azure-resource-manager/resource-group-overview.md)を参照してください。

1. Visual Studio を起動し、Azure アカウントでサインインします。 

1. **[ファイル]** メニューで、**[新規作成]** > **[プロジェクト]** の順に選択します  

   ![Visual Studio で新しいプロジェクトを作成する](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **[インストール済み]** で、**[Visual C#]** または **[Visual Basic]** を選択します。 **[クラウド]** > **[Azure リソース グループ]** の順に選択します。

   **[クラウド]** カテゴリまたは **[Azure リソース グループ]** プロジェクトが存在しない場合は、Azure SDK for Visual Studio をインストールしたことを確認してください。

1. プロジェクトの名前と場所を指定して、**[OK]** を選択します。 

   テンプレートを選択するように求められます。 
   空白、ロジック アプリ、または他のテンプレートで始めることもできますが、この例では、ロジック アプリと Azure 関数の呼び出しを含むサーバーレス アプリを構築するための Azure クイックスタート テンプレートを使用します。

   Visual Studio でロジック アプリのみを作成するには、**ロジック アプリ** テンプレートを選択します。 このテンプレートでは、ソリューションを Azure リソース グループに事前に展開する必要なしにロジック アプリ デザイナーで開かれる空のロジック アプリが作成されます。

1. **[この場所からテンプレートを参照します]** で、**[Azure クイックスタート (github/Azure/azure-quickstart-templates)]** を選択します。 

1. 検索ボックスにフィルターとして「logic-app」と入力し、**101-logic-app-and-function-app** サーバーレス クイックスタート テンプレートを選択して、**[OK]** を選択します。

   ![Azure クイックスタート テンプレートを選択する](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio で、リソース グループ プロジェクト用のソリューションが作成されて開かれます。 
   選択したクイック スタート テンプレートにより、リソース グループ プロジェクト内に `azuredeploy.json` という名前の配置テンプレートが作成されます。 
   この配置テンプレートには、HTTP 要求でトリガーし、Azure 関数を呼び出し、HTTP 応答として結果を返す、簡単なロジック アプリの定義が含まれています。 
   
   ![新しいサーバーレス ソリューション](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 次に、配置テンプレートを開いてサーバーレス アプリのリソースを確認するには、先にソリューションを Azure に展開する必要があります。 

## <a name="deploy-your-solution"></a>ソリューションのデプロイ

Visual Studio でロジック アプリ デザイナーを使用してロジック アプリを開く前に、Azure リソース グループが Azure に既に展開されている必要があります。 これにより、デザイナーはロジック アプリ内のリソースやサービスへの接続を作成できます。 このタスクでは、Visual Studio から Azure portal にソリューションを展開します。

1. ソリューション エクスプローラーでプロジェクトのショートカット メニューを開き、**[配置]** > **[新規]** の順に選択します。

   ![リソース グループの新しい展開を作成する](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. まだ選択していない場合は、展開する Azure サブスクリプションとリソース グループを選択します。 **[配置]** を選択します。

   ![[配置の設定]](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. **[パラメーターの編集]** ボックスが表示されたら、展開時にロジック アプリと Azure 関数に対して使用するリソース名を指定して、設定を保存します。 関数アプリに対してはグローバルに一意の名前を使用する必要があります。

   ![ロジック アプリと関数アプリの名前を指定する](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio で指定したリソース グループへの展開が始まると、Visual Studio の **[出力]** ウィンドウにソリューションの展開の状態が表示されます。 
   展開が終了すると、ロジック アプリが Azure portal に存在するようになります。

## <a name="edit-logic-app-in-visual-studio"></a>Visual Studio でロジック アプリを編集する

ソリューションがリソース グループに展開されたので、ロジック アプリを編集して変更できるように、ロジック アプリ デザイナーでロジック アプリを開きます。

1. ソリューション エクスプローラーで `azuredeploy.json` ファイルのショートカット メニューを開き、**[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します。

   ![ロジック アプリ デザイナーで "azuredeploy.json" を開く](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. **[ロジック アプリのプロパティ]** ボックスが表示されたら、まだ選択されていない場合は、**[サブスクリプション]** で Azure サブスクリプションを選択します。 **[リソース グループ]** で、ソリューションを展開したリソース グループと場所を選択して、**[OK]** を選択します。

   ![ロジック アプリのプロパティ](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   ロジック アプリ デザイナーが開いたら、手順を追加したりワークフローを変更したりして、更新を保存します。

   ![ロジック アプリ デザイナーで開かれたロジック アプリ](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Azure Functions プロジェクトを作成する

JavaScript、Python、F#、PowerShell、Batch、または Bash を使用して Functions プロジェクトと関数を作成するには、記事「[Azure Functions Core Tools の操作](../azure-functions/functions-run-local.md)」の手順に従ってください。 ソリューション内で C# を使用して Azure 関数を開発するには、記事「[Publish a .NET class library as a Function App](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)」(関数アプリとして .NET クラス ライブラリを発行する) の手順に従って C# クラス ライブラリを使用できます。

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio から関数を展開する

ここで使用している配置テンプレートでは、`azuredeploy.json` ファイルの変数で指定されている Git リポジトリから、ソリューション内に作成した Azure 関数が展開されます。 ソリューションで Functions プロジェクトを作成した場合、GitHub や Visual Studio Team Services などの Git ソース管理にそのプロジェクトをチェックインしてから、テンプレートがその Azure 関数を展開するように `repo` 変数を更新します。

## <a name="manage-logic-apps-and-view-run-history"></a>ロジック アプリを管理して実行履歴を表示する

Azure に既に展開されているロジック アプリについても、Visual Studio から編集、管理、実行履歴の表示、無効化を行うことができます。 

1. Visual Studio の **[表示]** メニューで、**[Cloud Explorer]** を選択します。 

1. **[すべてのサブスクリプション]** で、管理するロジック アプリに関連付けられている Azure サブスクリプションを選択して、**[適用]** を選択します。

1. **[Logic Apps]** でロジック アプリを選択します。 アプリのショートカット メニューで、**[Logic App Editor で開く]** を選択します。 

既に発行されているロジック アプリをリソース グループ プロジェクトにダウンロードできます。 したがって、Azure portal でロジック アプリの使用を始めた場合でも、Visual Studio でそのアプリをインポートして管理できます。 詳しくは、「[Visual Studio でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-visual-studio.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [サーバーレスのソーシャル ダッシュ ボードを構築する](logic-apps-scenario-social-serverless.md)
* [Visual Studio でロジック アプリを管理する](manage-logic-apps-with-visual-studio.md)
* [ロジック アプリのワークフロー定義言語](logic-apps-workflow-definition-language.md)
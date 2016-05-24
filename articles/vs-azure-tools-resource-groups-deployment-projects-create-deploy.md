<properties
   pageTitle="Visual Studio の Azure リソース グループ プロジェクトの作成とデプロイ | Microsoft Azure"
   description="Visual Studio を使用して、Azure リソース グループ プロジェクトを作成し、リソースを Azure にデプロイします。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor="" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="tomfitz" />

# Visual Studio での Azure リソース グループの作成とデプロイ

Visual Studio および [Azure SDK](https://azure.microsoft.com/downloads/) では、インフラストラクチャとコードを Azure にデプロイするプロジェクトを作成することができます。たとえば、アプリ用に Web ホスト、Web サイト、およびデータベースを定義し、そのインフラストラクチャをコードと共にデプロイできます。または、仮想マシン、仮想ネットワーク、およびストレージ アカウントを定義し、そのインフラストラクチャを、仮想マシンで実行するスクリプトと共にデプロイすることができます。**Azure リソース グループ** デプロイ プロジェクトでは、必要なすべてのリソースを反復可能な単一の操作でデプロイできます。リソースのデプロイと管理の詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。

Azure リソース グループ プロジェクトには、Azure にデプロイされるリソースを定義する Azure リソース マネージャー JSON テンプレートが含まれています。リソース マネージャーのテンプレートの要素の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。Visual Studio では、これらのテンプレートを編集することができ、テンプレートの操作を容易にするツールがサポートされています。

このトピックでは、Web アプリと SQL Database をデプロイします。ただし、デプロイの手順は、どの種類のリソースでもほとんど同じです。仮想マシンおよびこれに関連するリソースも同じように簡単にデプロイできます。Visual Studio では、一般的なシナリオのデプロイに適したさまざまなスターター テンプレートを多数用意しています。

## Azure リソース グループ プロジェクトを作成する

この手順では、**[Web アプリ + SQL]** テンプレートを使用して Azure リソース グループ プロジェクトを作成します。

1. Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順に選択し、**[C#]** または **[Visual Basic]** を選択します。次に **[Cloud]** を選択し、**[Azure リソース グループ]** プロジェクトを選択します。

    ![Cloud Deployment プロジェクト](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Azure リソース マネージャーにデプロイするテンプレートを選択します。デプロイするプロジェクトの種類に応じて、さまざまなオプションがあります。このトピックでは、**[Web アプリ + SQL]** テンプレートを選択します。

    ![テンプレートの選択](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    選択したテンプレートは出発点にすぎません。リソースを追加したり削除したりしてシナリオの要件を満たすことができます。

    >[AZURE.NOTE] 使用可能なテンプレートの一覧はオンラインで取得されるため、変更される可能性があります。

    Visual Studio では、Web アプリと SQL データベース用のリソース グループ デプロイ プロジェクトが作成されます。

1. デプロイ プロジェクト内のノードを展開して、作成された内容を確認します。

    ![ノードの表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    この例では [Web アプリ + SQL] テンプレートを選択したため、次のファイルが表示されます。

    |ファイル名|説明|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Azure リソース マネージャーにデプロイするための PowerShell コマンドを呼び出す PowerShell スクリプト。<br />**注** この PowerShell スクリプトは、テンプレートをデプロイするために Visual Studio によって使用されます。このスクリプトに変更を加えると Visual Studio でのデプロイにも影響するため、注意が必要です。|
    |WebSiteSQLDatabase.json|Azure にデプロイするインフラストラクチャと、デプロイ中に指定できるパラメーターを定義したリソース マネージャーのテンプレートです。正しい順序でデプロイされるようにリソース間の依存性も定義できます。|
    |WebSiteSQLDatabase.parameters.json|テンプレートで必要な値を含むパラメーター ファイルです。これらは、各デプロイをカスタマイズするために渡す値です。|
    |AzCopy.exe|ローカル ストレージのドロップ パスからファイルをストレージ アカウントのコンテナーにコピーする PowerShell スクリプトで使用されるツール。このツールは、テンプレートと共にコードをデプロイするようデプロイ プロジェクトを構成する場合にのみ使用します。|

    すべてのリソース グループ デプロイ プロジェクトに、上記の 4 つの基本的なファイルが含まれます。他のプロジェクトには、他の機能をサポートするために追加のファイルが含まれることがあります。

## リソース マネージャーのテンプレートをカスタマイズする

デプロイするリソースが記述されている JSON テンプレートを変更すると、デプロイ プロジェクトをカスタマイズできます。JSON とは JavaScript Object Notation の略であり、操作が簡単なシリアル化されたデータ形式です。JSON ファイルでは、各ファイルの上部で参照されているスキーマが使用されます。スキーマをより深く理解する場合は、スキーマをダウンロードして分析できます。スキーマでは、許可される要素、フィールドの種類と形式、列挙値に使用できる値などが定義されています。リソース マネージャーのテンプレートの要素の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

テンプレートで作業するには、**WebSiteSQLDatabase.json** を開きます。

Visual Studio エディターでは、リソース マネージャーのテンプレートの編集に役立つツールを提供しています。**[JSON のアウトライン]** ウィンドウを使用すると、テンプレートで定義されている要素を簡単に確認できます。

![JSON アウトラインの表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

アウトライン内の任意の要素を選択すると、テンプレートの該当部分に移動し、対応する JSON が強調表示されます。

![JSON の移動](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

テンプレートには新しいリソースを追加することができます。それには、[JSON のアウトライン] ウィンドウの上部にある **[リソースの追加]** ボタンを選択するか、または **[リソース]** を右クリックし **[新しいリソースの追加]** を選択します。

![リソースの追加](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

このチュートリアルでは、**[ストレージ アカウント]** を選択し、それに名前を付けます。ストレージ アカウント名は数字と小文字のみで構成され、文字長が 24 文字未満となる必要があります。指定した文字にはプロジェクトによって 13 文字の一意の文字列が追加されます。したがって、必ず 11 文字未満の名前を指定してください。

![ストレージの追加](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

リソースだけでなく、ストレージ アカウントの種類を示すパラメーターとストレージ アカウントの名前を示す変数も追加されています。

![アウトラインの表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

**WebSitePicturesType** パラメーターでは、許可する種類と既定の種類があらかじめ定義されています。これらの値は、そのまま使用することも、シナリオに合わせて編集することもできます。このテンプレートを使用して **Premium\_LRS** ストレージ アカウントをデプロイするのを任意のユーザーに許可したくない場合は、許可する種類からそれを削除します。

    "WebSitePicturesType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio では、テンプレートの編集時にどんなプロパティが使用できるのかを容易に理解できるように intellisense を提供しています。たとえば、App Service プランのプロパティを編集するには、**HostingPlan** リソースに移動し、**properties** の新しい値を追加します。Intellisense では、使用できる値を示し、その値に関する説明を提供します。

![IntelliSense の表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

**numberOfWorkers** は 1 に設定することができます。

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## リソース グループ プロジェクトを Azure にデプロイする

これでプロジェクトをデプロイする準備が整いました。Azure リソース グループ プロジェクトをデプロイする場合は、Azure のリソース (Web アプリやデータベースなど) の単なる論理グループである Azure リソース グループにプロジェクトをデプロイします。

1. デプロイ プロジェクト ノードのショートカット メニューで、**[デプロイ]**、**[新しい配置]** の順に選択します。

    ![メニュー項目 [配置]、[新しい配置]](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    **[リソース グループに配置する]** ダイアログ ボックスが表示されます。

    ![[リソース グループに配置する] ダイアログ ボックス](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. **[リソース グループ]** ボックスの一覧で、既存のリソース グループを選択するか、新しいリソース グループを作成します。リソース グループを作成するには、**[リソース グループ]** ボックスの一覧を開き、**[<Create New...>]** を選択します。

    ![[リソース グループに配置する] ダイアログ ボックス](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    **[リソース グループの作成]** ダイアログ ボックスが表示されます。グループの名前と場所を指定し、**[作成]** ボタンを選択します。

    ![[リソース グループの作成] ダイアログ ボックス](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. **[パラメーターの編集]** ボタンを選択すると、デプロイ用のパラメーターを編集できます。パラメーターの値を指定し、**[保存]** ボタンを選択します。

    ![[パラメーターの編集] ダイアログ ボックス](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    **[パスワードの保存]** オプションを選択すると、パスワードは JSON ファイルにプレーン テキストとして保存されます。この設定はセキュリティ上安全ではありません。

1. **[デプロイ]** をクリックして、プロジェクトを Azure にデプロイします。デプロイの進行状況は、**出力**ウィンドウで確認できます。デプロイは、構成に応じて、完了までに数分かかる場合があります。求められた場合は、データベース管理者のパスワードを入力します。

    >[AZURE.NOTE] Azure PowerShell コマンドレットのインストールを求められる場合があります。これらのコマンドレットは、Azure リソース グループをデプロイするために必要であるため、インストールする必要があります。
    
1. デプロイが完了すると、**[出力]** ウィンドウに次のようなメッセージが表示されます。

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. ブラウザーで、[Azure ポータル](https://portal.azure.com/)を開き、アカウントにサインインします。リソース グループの内容を確認するには、**[リソース グループ]** を選択し、デプロイしたリソース グループを選択します。

    ![グループの選択](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. デプロイ済みのすべてのリソースが表示されます。

    ![リソースの表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. 変更を加え、プロジェクトを再デプロイする場合は、Azure リソース グループ プロジェクトのショートカット メニューから直接既存のリソース グループを選択できます。ショートカット メニューの **[デプロイ]** を選択してから、デプロイ先のリソース グループを選択します。

    ![デプロイされた Azure リソース グループ](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## インフラストラクチャでコードをデプロイする

この時点で、アプリ用のインフラストラクチャはデプロイ済みですが、実際のコードはプロジェクトでデプロイされていません。このトピックでは、デプロイ時に Web アプリと SQL Database テーブルをデプロイする方法を示します。Web アプリではなく仮想マシンをデプロイする場合は、デプロイの一環としてマシン上で何らかのコードを実行します。Web アプリのコードをデプロイするプロセスまたは仮想マシンを設定するプロセスは、ほぼ同じです。

1. Visual Studio ソリューションで、**ASP.NET Web Application** を追加します。 

    ![Web アプリの追加](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. **[MVC]** を選択し、**[クラウドでホストする]** のフィールドをオフにします。リソース グループ プロジェクトで、そのタスクは実行されないためです。

    ![MVC の選択](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Web アプリが作成された後、リソース グループ プロジェクトの参照を Web アプリ プロジェクトに追加します。

    ![参照の追加](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    参照を追加することで、リソース グループ プロジェクトに Web アプリ プロジェクトをリンクし、3 つのキー プロパティを自動的に設定します。
    
    - **[追加のプロパティ]** には Azure Storage にプッシュされる Web デプロイ パッケージのステージングの場所が含まれます。 
    - **[ファイル パスを含める]** にはパッケージを作成するパスが含まれます。**[ターゲットを含める]** にはデプロイで実行するコマンドが含まれます。 
    - **Build;Package** の既定値を使用すると、デプロイでは、Web デプロイ パッケージ (package.zip) がビルドおよび作成されます。  
    
    パッケージを作成するのに必要な情報はプロパティから取得するので、デプロイで発行プロファイルは必要ありません。
    
      ![参照の表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. テンプレートに新しいリソースを追加します。今回は **[Web Deploy for Web Apps]** を選択します。

    ![Web デプロイの追加](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. リソース グループにリソース グループ プロジェクトを再デプロイします。今回は、新しいパラメーターがいくつか存在します。**\_artifactsLocation** または **\_artifactsLocationSasToken** の値は自動的に生成されるので、指定する必要はありません。デプロイ パッケージが含まれているパスにフォルダーとファイル名を設定します。

    ![Web デプロイの追加](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    **[成果物のストレージ アカウント]** には、このリソース グループでデプロイしたものを使用できます。
    
デプロイの完了後、サイトを参照すると、既定の ASP.NET アプリがデプロイされていることがわかります。

![デプロイされたアプリの表示](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## 次のステップ

- ポータルを使用したリソース管理の詳細については、「[Azure ポータルを使用した Azure リソースの管理](./azure-portal/resource-group-portal.md)」を参照してください。
- テンプレートの詳細については、「[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->
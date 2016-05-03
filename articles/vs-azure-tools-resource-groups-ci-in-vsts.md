<properties
	pageTitle="Azure リソース グループのプロジェクトを使用した VS Team Services での継続的インテグレーション | Microsoft Azure"
	description="Visual Studio で Azure リソース グループのデプロイメント プロジェクトを使用して、Visual Studio Team Services での継続的インテグレーションを設定する方法を説明します。"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="04/19/2016"
	ms.author="tarcher" />

# Azure リソース グループのデプロイメント プロジェクトを使用した Visual Studio Team Services での継続的インテグレーション

Azure テンプレートをデプロイするには、さまざまな段階 (ビルド、テスト、Azure へのコピー ("ステージング" とも呼ばれる)、テンプレートのデプロイ) を通してタスクを実行する必要があります。Visual Studio Team Services (VS Team Services) でこれを行うには、2 つの方法があります。どちらの方法でも同じ結果になるため、ワークフローに最適な方法を選択します。

-	Azure リソース グループ デプロイメント プロジェクト (Deploy-AzureResourceGroup.ps1) に含まれている PowerShell スクリプトを実行するビルド定義にシングル ステップを追加します。このスクリプトではアーティファクトをコピーして、テンプレートをデプロイします。
-	それぞれが単一のタスクを実行する、複数の VS Team Services のビルド ステップを追加します。

この記事では、最初のオプション (ビルド定義を使用して PowerShell スクリプトを実行する) の使用方法を示します。このオプションの利点は、Visual Studio で開発者が使用するスクリプトが、VS Team Services で使用されるスクリプトと同じであるということです。この手順では、Visual Studio デプロイメント プロジェクトが VS Team Services にチェックインしていることを前提としています。

## Azure へのアーティファクトのコピー 

シナリオに関係なく、テンプレート デプロイメントに必要なアーティファクトがある場合、このアーティファクトに Azure リソース マネージャーのアクセス権を付与する必要があります。これらのアーティファクトには、次のファイルを含めることができます。

-	入れ子になったテンプレート
-	構成スクリプトと DSC スクリプト
-	アプリケーションのバイナリ

### 入れ子になったテンプレートと構成スクリプト
Visual Studio が提供する (または、Visual Studio のスニペットでビルドされた) テンプレートを使用すると、PowerShell スクリプトはアーティファクトをステージングするだけでなく、さまざまなデプロイメントのリソースに対する URI もパラメーター化します。次に、スクリプトはアーティファクトを Azure のセキュリティで保護されたコンテナーにコピーし、そのコンテナーの SaS トークンを作成して、テンプレート デプロイメントにその情報を渡します。入れ子になったテンプレートの詳細については、「[テンプレート デプロイメントの作成](https://msdn.microsoft.com/library/azure/dn790564.aspx)」を参照してください。

## VS Team Services での継続的なデプロイメントの設定

VS Team Services で PowerShell スクリプトを呼び出すには、ビルド定義を更新する必要があります。簡単に言うと、手順は次のとおりです。

1.	ビルド定義を編集します。
1.	VS Team Services で Azure 認証を設定します。
1.	Azure リソース グループ デプロイメント プロジェクトで PowerShell スクリプトを参照する Azure PowerShell のビルド ステップを追加します。
1.	*-ArtifactsStagingDirectory* パラメーターの値を設定し、VS Team Services に組み込まれているプロジェクトを操作します。

### 詳細なチュートリアル

次の手順では、VS Team Services で継続的なデプロイメントを構成するために必要な手順を説明します。

1.	VS Team Services のビルド定義を編集し、Azure PowerShell のビルド ステップを追加します。**[ビルド定義]** カテゴリでビルド定義を選択し、**[編集]** リンクを選択します。

    ![][0]

1.	新しい **Azure PowerShell** のビルド ステップをビルド定義に追加して、**[ビルド ステップの追加...]** ボタンを選択します。

    ![][1]

1.	**[タスクのデプロイ]** カテゴリを選択し、**Azure PowerShell** タスクを選択して、**[追加]** ボタンをクリックします。

    ![][2]

1.	**Azure PowerShell** のビルド ステップを選択して、その値を入力します。

    1.	既に Azure サービス エンドポイントが VS Team Services に追加されている場合、**[Azure Subscription]** ドロップダウン リスト ボックスでサブスクリプションを選択して、次のセクションにスキップします。 

        VS Team Services に Azure サービス エンドポイントがない場合、1 つ追加する必要があります。この項では、プロセスを示します。Azure アカウントが Microsoft アカウント (Hotmail など) を使用する場合、サービス プリンシパルの認証を取得するために、次の手順を実行する必要があります。

    1.	**[Azure サブスクリプション]** ドロップダウン リスト ボックスの横にある **[管理]** リンクを選択します。

        ![][3]

    1. **[新しいサービス エンドポイント]** ドロップダウン リスト ボックスで **[Azure]** を選択します。

        ![][4]

    1.	**[Azure サブスクリプションの追加]** ダイアログ ボックスで、**[サービス プリンシパル]** オプションを選択します。

        ![][5]

    1.	Azure サブスクリプション情報を **[Azure サブスクリプションの追加]** ダイアログ ボックスに追加します。次の項目を指定する必要があります。
        -	サブスクリプション ID
        -	サブスクリプション名
        -	サービス プリンシパル ID
        -	サービス プリンシパル キー
        -	テナント ID

    1.	任意の名前を **[サブスクリプション名]** ボックスに追加します。この値は、後で VS Team Services の **[Azure サブスクリプション]** ドロップダウン リストに表示されます。

    1.	Azure サブスクリプション ID がわからない場合は、次のいずれかのコマンドを使用して取得することができます。
        
        PowerShell スクリプトでは、次を使用します。

        `Get-AzureRmSubscription`

        Azure CLI では、次を使用します。

        `azure account show`
    

    1.	サービス プリンシパル ID、サービス プリンシパル キー、およびテナント ID を取得するには、「[ポータルを使用して Active Directory アプリケーションとサービス プリンシパルを作成する](resource-group-create-service-principal-portal.md)」または「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」の手順を行います。

    1.	サービス プリンシパル ID、サービス プリンシパル キー、およびテナント ID の値を **[Azure サブスクリプションの追加]** ダイアログ ボックスに追加し、**[OK]** ボタンをクリックします。

        これで、Azure PowerShell スクリプトを実行するために使用する有効なサービス プリンシパルがあります。

1.	ビルド定後を編集して、**Azure PowerShell** のビルド ステップを選択します。**[Azure サブスクリプション**] ドロップダウン リスト ボックスでサブスクリプションを選択します。(サブスクリプションが表示されない場合、**[管理]** リンクの横にある **[更新]** ボタンをクリックします)。

    ![][8]

1.	Deploy-AzureResourceGroup.ps1 のPowerShell スクリプトのパスを指定します。これを行うには、**[スクリプト パス]** ボックスの横にある省略記号 (...) ボタンを選択して、プロジェクトの **[スクリプト]** フォルダーの Deploy-AzureResourceGroup.ps1 PowerShell スクリプトに移動し、それを選択して、**[OK]** ボタンをクリックします。

    ![][9]

1. スクリプトを選択した後、Build.StagingDirectory (*ArtifactsLocation* の設定と同じディレクトリ) から実行できるように、スクリプトのパスを更新します。“$(Build.StagingDirectory)/” をスクリプト パスの先頭に追加して、これを実行することができます。

    ![][10]

1.	**[スクリプトの引数]** ボックスに、次のパラメーター (1 行) を入力します。Visual Studio でスクリプトを実行すると、**[出力]** ウィンドウで　VS がパラメーターを使用する方法を確認することができます。ビルド ステップでパラメーター値を設定するための出発点として、この方法を使用できます。

    | パラメーター | 説明|
    |---|---|
    | -ResourceGroupLocation | **eastus** や **'East US'** など、リソース グループが配置される geo ロケーションの値です。(名前にスペースが含まれる場合は、一重引用符を追加します)。 詳細については、「[Azure のリージョン](https://azure.microsoft.com/regions/)」を参照してください。| |
    | -ResourceGroupName | このデプロイメントに使用するリソース グループの名前。| |
    | -UploadArtifacts | このパラメーターは、存在する場合、アーティファクトがローカル システムから Azure にアップロードする必要があることを指定します。テンプレート デプロイメントに、PowerShell スクリプト (構成スクリプトや入れ子になったテンプレートなど) を使用してステージングする追加のアーティファクトが必要な場合にのみ、このスイッチを設定する必要があります。 |
    | -StorageAccountName | このデプロイメントのアーティファクトをステージングするためのストレージ アカウントの名前。Azure にアーティファクトをコピーしている場合にのみ、このパラメーターが必要です。このストレージ アカウントはデプロイメントで自動的に作成されることはありません。既に存在している必要があります。| |
    | -StorageAccountResourceGroupName | ストレージ アカウントに関連付けられているリソース グループの名前。Azure にアーティファクトをコピーしている場合にのみ、このパラメーターが必要です。| |
    | -TemplateFile | Azure リソース グループのデプロイメント プロジェクト内のテンプレート ファイルへのパス。柔軟性を高めるには、絶対パスの代わりに、PowerShell スクリプトの場所に関連するこのパラメーターのパスを使用します。|
    | -TemplateParametersFile | Azure リソース グループのデプロイメント プロジェクト内のパラメーター ファイルへのパス。柔軟性を高めるには、絶対パスの代わりに、PowerShell スクリプトの場所に関連するこのパラメーターのパスを使用します。|
    | -ArtifactStagingDirectory | このパラメーターを使用すると、PowerShell スクリプトが、プロジェクトのバイナリ ファイルがコピーされる必要のあるフォルダーを認識することができます。この値は、PowerShell スクリプトが使用する既定値を上書きします。VS Team Services を使用する場合、次の値を設定します: -ArtifactStagingDirectory $(Build.StagingDirectory) |

    スクリプト引数の例を次に示します (読みやすくするために改行しています)。

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    完了すると、**[スクリプトの引数]** ボックスは次のようになります。

    ![][11]

1.	Azure PowerShell のビルドのステップにすべての必須項目を追加したら、**[キュー]** ビルド ボタンを選択してプロジェクトをビルドします。**[ビルド]** 画面には、PowerShell スクリプトからの出力が表示されます。

## 次のステップ

Azure リソース マネージャーと Azure リソース グループの詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0420_2016-->
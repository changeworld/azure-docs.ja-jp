<properties 
   pageTitle="Visual Studio でのリソース マネージャー テンプレートの編集 | Microsoft Azure"
   description="Visual Studio を使用して Azure リソース マネージャー テンプレートにリソースを追加する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor="" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="11/13/2015"
   ms.author="tomfitz" />

# Visual Studio でのリソース マネージャー テンプレートの編集

Visual Studio では、リソース グループのリソース マネージャー テンプレートを編集できます。Visual Studio の [JSON アウトライン] ウィンドウでテンプレートを変更するか、テンプレートの構文を直接変更します。

## [JSON アウトライン] ウィンドウでのリソース グループへのリソースの追加

### リソース グループにリソースを追加するには

1. ソリューション エクスプローラーで、Azure リソース グループの JSON ファイルを選択します。エディターに JSON ファイルが表示され、エディターの横に **[JSON アウトライン]** ウィンドウも表示されます。[JSON アウトライン] ウィンドウを閉じると、JSON テンプレート ファイル (パラメーター ファイルではありません) のコンテキスト メニューで [アウトラインの表示] を選択するまでは、再度自動で開くことはありません。

    ![Azure リソース グループの JSON ファイル](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. **[JSON アウトライン]** ウィンドウで、**resources** ノードを選択し、コンテキスト メニューの **[新しいリソースの追加]** を選択するか、[JSON アウトライン] ウィンドウの上部にある **[リソースの追加]** ボタンをクリックします。

    ![リソース グループへの新しいリソースの追加](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. **[リソースの追加]** ダイアログ ボックスのリソースの一覧で、追加するリソースを選択し、リソースに必要な情報を指定してから **[追加]** ボタンをクリックします。たとえば、ストレージ アカウントを追加する場合は、作成されるパラメーターのベース名を指定する必要があります。
 
    ![[リソースの追加] ダイアログ ボックス](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    リソースは **[JSON アウトライン]** ウィンドウのリソース一覧に追加され、ファイルにリソースを表す新しい JSON が表示されます。関連するパラメーターや変数も追加される場合があります。


    ![JSON ファイルに追加されたリソース](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Azure リソース グループに新しいリソースをデプロイします。ソリューション エクスプローラーのリソース グループ プロジェクトのコンテキスト メニューで、**[デプロイ]** を選択し、リソース グループの名前を選択します。

    ![デプロイされた Azure リソース グループ](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    **[リソース グループに配置する]** ダイアログ ボックスが表示されます。


1. **[デプロイ]** ボタンをクリックします。

1. パラメーターをユーザーが指定する必要がある場合は、**[パラメーターの編集]** ダイアログ ボックスが表示されます。必要な値を入力して、**[保存]** ボタンをクリックします。新しいリソースが Azure リソース グループにデプロイされます。

## テンプレートの構文の編集

Visual Studio では、テンプレートを直接編集することもできます。テンプレートの値の編集を開始すると、指定できる値に関するヘルプがエディター内に表示されます。

![テンプレートの編集](./media/vs-azure-tools-resource-group-adding-resources/arm-edit-template.png)

テンプレートの構造の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

## 関連項目

[Visual Studio での Azure リソース グループの作成とデプロイ](vs-azure-tools-resource-groups-deployment-project-create-deploy.md)

[Azure リソース マネージャー コマンドレット](https://msdn.microsoft.com/library/azure/dn757692.aspx)

[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager/)

[Channel9 ビデオ: Azure リソース マネージャー](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=AcomDC_0128_2016-->
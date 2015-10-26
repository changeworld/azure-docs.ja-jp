<properties 
   pageTitle="Azure リソース グループへのリソースの追加"
   description="Visual Studio を使用して Azure リソース グループにリソースを追加する方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Azure リソース グループへのリソースの追加

リソース グループにさらにリソースを追加する必要がある場合は、Visual Studio の [JSONアウトライン] ウィンドウで追加できます。

## リソース グループへのリソースの追加

### リソース グループにリソースを追加するには

1. ソリューション エクスプローラーで、Azure リソース グループの JSON ファイルを選択します。エディターに JSON ファイルが表示され、エディターの横に **[JSON アウトライン]** ウィンドウも表示されます。[JSON アウトライン] ウィンドウを閉じると、JSON テンプレート ファイル (パラメーター ファイルではありません) のコンテキスト メニューで [アウトラインの表示] を選択するまでは、再度自動で開くことはありません。

    ![JSON file for Azure resource group](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. **[JSON アウトライン]** ウィンドウで、**resources** ノードを選択し、コンテキスト メニューの **[新しいリソースの追加]** を選択するか、[JSON アウトライン] ウィンドウの上部にある **[リソースの追加]** ボタンをクリックします。

    ![Adding a new resource to resource group](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. **[リソースの追加]** ダイアログ ボックスのリソースの一覧で、追加するリソースを選択し、リソースに必要な情報を指定してから **[追加]** ボタンをクリックします。たとえば、ストレージ アカウントを追加する場合は、作成されるパラメーターのベース名を指定する必要があります。
 
    ![Add Resource dialog box](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    リソースは **[JSON アウトライン]** ウィンドウのリソース一覧に追加され、ファイルにリソースを表す新しい JSON が表示されます。関連するパラメーターや変数も追加される場合があります。


    ![Resource added to JSON file](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Azure リソース グループに新しいリソースをデプロイします。ソリューション エクスプローラーのリソース グループ プロジェクトのコンテキスト メニューで、**[デプロイ]** を選択し、リソース グループの名前を選択します。

    ![Azure resource group deployed](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    **[リソース グループに配置する]** ダイアログ ボックスが表示されます。


1. **[デプロイ]** ボタンをクリックします。

1. パラメーターをユーザーが指定する必要がある場合は、**[パラメーターの編集]** ダイアログ ボックスが表示されます。必要な値を入力して、**[保存]** ボタンをクリックします。新しいリソースが Azure リソース グループにデプロイされます。

## 関連項目

[Azure リソース グループ デプロイ プロジェクトの作成とデプロイ](http://go.microsoft.com/fwlink/p/?LinkID=623073)

[Azure リソース マネージャー コマンドレット](https://msdn.microsoft.com/library/dn654592.aspx)

[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager/)

[Channel9 ビデオ: Azure リソース マネージャー](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=Oct15_HO3-->
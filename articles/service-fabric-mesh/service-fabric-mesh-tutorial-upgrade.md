---
title: チュートリアル - Azure Service Fabric Mesh アプリケーションをアップグレードする | Microsoft Docs
description: Visual Studio を使用して Service Fabric アプリケーションをアップグレードする方法を学習します
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 7985c8e9e26126040d842ded998a953281daa2ae
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953554"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>チュートリアル: Visual Studio を使用して Service Fabric アプリケーションをアップグレードする方法を学習する

このチュートリアルはシリーズの第 4 部です。ここでは、Azure Service Fabric Mesh アプリケーションを Visual Studio から直接アップグレードする方法について説明します。 アップグレードには、コードの更新と構成の更新の両方が含まれます。 Visual Studio 内からアップグレードする手順と発行する手順は同じであることがわかります。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio を使用して Service Fabric Mesh サービスをアップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Visual Studio で Service Fabric Mesh アプリを作成する](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [ローカル開発クラスター内で実行されている Service Fabric Mesh アプリをデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh アプリをデプロイする](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Service Fabric Mesh アプリをアップグレードする
> * [Service Fabric Mesh リソースをクリーンアップする](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* To Do アプリをデプロイしていない場合は、[Service Fabric Mesh Web アプリケーションの発行](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)に関するページの指示に従ってください。

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Visual Studio を使用して Service Fabric Mesh サービスをアップグレードする

この記事では、アプリケーション内でマイクロサービスを独立してアップグレードする方法を示します。  この例では、タスク カテゴリを表示するように `WebFrontEnd` サービスを変更します。 その後、デプロイされているサービスをアップグレードします。

## <a name="modify-the-config"></a>構成を変更する

アップグレードは、コードの変更、構成の変更、またはその両方のために行われることがあります。  構成の変更を導入するため、`WebFrontEnd` プロジェクトの `service.yaml` ファイルを開きます (**[サービス リソース]** ノードの下にあります)。

Web フロントエンドの使用量が多いことが予想されるため、`resources:` セクションで `cpu:` を 0.5 から 1.0 に変更します。 その結果、次のようになります。

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>モデルを変更する

コードの変更を導入するため、`ToDoItem.cs` ファイルで `ToDoItem` クラスに `Category` プロパティを追加します。

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

そして、同じファイル内の `Load()` メソッドを更新し、カテゴリを既定の文字列に設定します。

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>サービスを変更する

`WebFrontEnd` プロジェクトは、To-Do リスト項目を表示する Web ページを含む ASP.NET Core アプリケーションです。 `WebFrontEnd` プロジェクトで `Index.cshtml` を開き、タスクのカテゴリを表示するための次に示す 2 行を追加します。

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

アプリをビルドして実行し、タスクの一覧を示す新しいカテゴリ列が Web ページに表示されることを確認します。

## <a name="upgrade-the-app-from-visual-studio"></a>Visual Studio からアプリをアップグレードする

コードのアップグレードまたは構成のアップグレードのどちらを行う場合でも (この例では両方を行います)、Azure 上の Service Fabric Mesh アプリをアップグレードするには、Visual Studio で **todolistapp** を右クリックして、**[発行]** を選択します。

**[Service Fabric アプリケーションの発行]** ダイアログが表示されます。

![Visual Studio - Service Fabric mesh の発行ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure アカウントとサブスクリプションを選択します。 最初に To-Do アプリを Azure に発行するときに使用した場所を、確実に **[場所]** に設定します。 この記事では、**[米国東部]** を使用しました。

最初に To-Do アプリを Azure に発行するときに使用したリソース グループを、確実に **[リソース グループ]** に設定します。

最初に To-Do アプリを Azure に発行するときに作成した Azure Container Registry の名前を、確実に **[Azure Container Registry]** に設定します。

発行ダイアログで、**[発行]** ボタンをクリックして Azure 上の To-Do アプリをアップグレードします。

Visual Studio の **[出力]** ウィンドウで **[Service Fabric Tools]** ウィンドウを選択することで、アップグレードの進行状況を監視できます。 アップグレードが完了すると、**Service Fabric Tools** の出力に、アプリケーションの IP アドレスとポートが URL の形式で表示されます。

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Web ブラウザーを開き、その URL に移動して、Azure で実行されている Web サイトを表示します。 カテゴリ列が含まれる Web ページが表示されるはずです。

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio を使用して Service Fabric Mesh アプリをアップグレードする方法

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh リソースをクリーンアップする](service-fabric-mesh-tutorial-cleanup-resources.md)
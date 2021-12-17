---
title: '方法: Azure Fluid Relay サービスをプロビジョニングする'
description: Azure Portal を使用して Azure Fluid Relay サービスをプロビジョニングする方法
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 29397802e0f4e4c4a47e82db8c1ef17bc89c7d9a
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661579"
---
# <a name="how-to-provision-an-azure-fluid-relay-service"></a>方法: Azure Fluid Relay サービスをプロビジョニングする

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

アプリを Azure Fluid Relay サーバーに接続する前に、Azure アカウントで Azure Fluid Relay サーバー リソースをプロビジョニングする必要があります。 この記事では、Azure Fluid Relay サービスをプロビジョニングして、すぐに使用できるようにするための手順について説明します。 

## <a name="prerequisites"></a>前提条件

Azure Fluid Relay サービスを作成するには、Azure アカウントが必要です。 アカウントをお持ちでない場合は、[Azure を無料でお試しいただけます](https://azure.com/free)。

## <a name="create-a-resource-group"></a>リソース グループを作成する
リソース グループは、Azure リソースの論理的なコレクションです。 すべてのリソースのデプロイと管理はリソース グループで行われます。 リソース グループを作成するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 左側のナビゲーションで、 **[リソース グループ]** を選択します。 その後、 **[追加]** を選択します。

    :::image type="content" source="../images/add-resource-group.png" alt-text="Azure portal の [リソース グループ] ページのスクリーンショット。":::

3. [サブスクリプション] で、リソース グループを作成したい Azure サブスクリプションの名前を選択します。

    :::image type="content" source="../images/create-resource-group.png" alt-text="Azure portal の [リソース グループの作成] ページのスクリーンショット。":::

1. リソース グループに一意の名前を入力します。 現在選択されている Azure サブスクリプションでその名前を使用できるかどうかが、すぐに自動で確認されます。
1. リソース グループのリージョンを選択します。
1. **[確認および作成]** を選択します。
1. [確認および作成] ページで、 **[作成]** を選択します。

## <a name="create-a-fluid-relay-resource"></a>Fluid Relay リソースを作成する
各 Azure Fluid Relay サーバー リソースにより、Fluid アプリケーションで使用するためのテナントが提供されます。 そのテナント内に、多数のコンテナーやセッションを作成できます。 ポータルを使用してリソース グループに Fluid Relay を作成するには:

1. Azure portal で、画面の左上にある **[リソースの作成]** を選択します。
2. "Fluid" を検索します
 
    :::image type="content" source="../images/marketplace-fluid-relay.png" alt-text="&quot;Fluid&quot; の検索結果が含まれる [リソースの作成] ページのスクリーンショット。":::

3. **Fluid Relay** を選択して、 **[作成]** を選択します。
 
    :::image type="content" source="../images/fluid-relay-details-page.png" alt-text="Azure Fluid Relay のマーケットプレース詳細ページのスクリーンショット。":::

4. [作成] ページで、以下の手順のようにします。

    :::image type="content" source="../images/create-fluid-relay-server.png" alt-text="新しい Azure Fluid Relay サーバーを構成する方法のスクリーンショット。":::

    1. 名前空間を作成するサブスクリプションを選択します。
    2. 前の手順で作成したリソース グループを選択します。
    3. Fluid Relay リソースの名前を入力します。
    4. 名前空間の場所を選択します。
    
    > [!NOTE]
    > パブリック プレビュー期間中は、米国西部 2、西ヨーロッパ、東南アジア リージョンのみがサポートされます

5. ページの下部にある **[確認および作成]** ボタンをクリックします。

6. [確認および作成] ページで、設定を確認し、 *[作成]* を選択します。 デプロイが完了するまで待ちます。

    :::image type="content" source="../images/create-server-validation-complete.png" alt-text="検証が正常に完了した後の新しいサービスのページのスクリーンショット。":::

7. [デプロイ] ページで、 **[リソースに移動]** を選択して、対象の名前空間のページに移動します。

    :::image type="content" source="../images/deployment-complete.png" alt-text="デプロイが完了したことを示す Azure portal のスクリーンショット。":::

8. 次の例のような Fluid Relay ページが表示されていることを確認します。

    :::image type="content" source="../images/resource-details.png" alt-text="デプロイされた Fluid Relay リソースの詳細ページの例のスクリーンショット。":::

## <a name="next-steps"></a>次の手順
ここでは、リソース グループを作成し、そのグループに Azure Fluid Relay リソースをプロビジョニングしました。 次に、[アプリで Azure Fluid Relay サービスに接続する](../quickstarts/quickstart-dice-roll.md)ことができます。

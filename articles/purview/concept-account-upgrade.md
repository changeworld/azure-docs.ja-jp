---
title: Azure Purview アカウントのアップグレード情報
description: 2021 年 8 月 18 日より前に作成された Azure Purview アカウントは、Purview の最新バージョンに自動的にアップグレードされます。 この記事では、変更内容と、次に必要な手順について説明します。
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: b528e4a4b96e0dbe5f8b2eca748420a23f3c2950
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208902"
---
# <a name="azure-purview-account-upgrade-information"></a>Azure Purview アカウントのアップグレード情報

Azure Purview では、2021 年 8 月 18 日にエラスティック データ マップ、コレクションの更新、アクセス制御の更新などの新機能がリリースされました。 2021 年 8 月 18 日より **後** に作成された Azure Purview アカウントには、これらのすべての新機能が既に組み込まれています。

2021 年 8 月 18 日より **前** に作成されたアカウント (レガシ アカウント) は、自動的にアップグレードされて、これらの機能が組み込まれます。

2021 年 8 月 18 日より **前** に作成された Purview アカウントをお持ちの場合は、その Purview アカウントがアップグレードされるとメールで通知されます。

このドキュメントでは、アカウントのアップグレード後に目にすることになる変更と、新機能を使用するために必要な手順について説明します。

## <a name="elastic-data-map"></a>エラスティック データ マップ

アップグレードされた Azure Purview アカウントでは、使用状況に応じて動的にスケーリングが行われる、エラスティック データ マップという新しいデータ マップ システムが使用されます。 エラスティック データ マップは、1 容量ユニットで開始され、必要に応じて容量ユニットが追加されたり削減されたりします。
この新機能によって Purview の操作方法に直接的な影響を及ぶことはありませんが、課金には影響があります。 データ環境の管理に必要なサイズに合わせてスケーリングされ、使用した分だけ課金されます。

新しいエラスティック データ マップとその課金方法の詳細については、[エラスティック データ マップに関するページ](concept-elastic-data-map.md)をご覧ください。

Purview 全体の料金の詳細については、[料金計算ツール](https://azure.microsoft.com/pricing/details/azure-purview/)をご覧ください。

アカウントがアップグレードされたら、エラスティック データ マップを使用するための変更を行う必要はありません。 自動的に有効になります。

## <a name="collections"></a>コレクション

コレクションは Purview レガシ アカウントに存在していますが、アップグレードされた Purview アカウントでは新しい機能が設けられており、管理方法が異なります。

[レガシ コレクション](how-to-create-and-manage-collections.md#legacy-collection-guide)は、Purview アカウントのデータ ソースと成果物を整理する手段でした。 コレクションは、ビジネス環境に合わせて Purview データ マップをカスタマイズするために引き続き使用されますが、アクセス制御もこれに組み込まれました。 コレクションを使用すると、データ マップの外部で大まかにアクセスを制御するのではなく、データ マップに合ったアクセス管理を実現できます。

[コレクション](how-to-create-and-manage-collections.md)を使用することで、データ ソースだけでなく、検出可能性をきめ細かく管理できます。 ユーザーは、自分にアクセス権のあるコレクション内の資産のみを表示できるため、自分が必要とする情報のみを参照できます。

Purview アカウントがアップグレードされると、コレクションも更新されます。 現在の資産はすべて、これらの新しいコレクションに移行されます。 以下のセクションでは、コレクションと既存の資産が表示される場所について説明します。

### <a name="locate-and-manage-collections"></a>コレクションを見つけて管理する

新しいコレクションを見つけるには、[Purview Studio](https://web.purview.azure.com/resource/) にアクセスします。 Studio を見つけるには、[Azure portal](https://portal.azure.com) で Purview リソースに移動し、概要ページで **[Purview Studio を開く]** タイルを選択します。

左ペインから [データ マップ] > [コレクション] を選択し、コレクション管理ページを開きます。

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[コレクション] タブが選択されています。" border="true":::

ここには、ルート コレクションと、すべての既存のコレクションが表示されます。 ルート コレクションは、コレクション リストの一番上のコレクションであり、Purview リソースと同じ名前になります。 次の例では、Contoso Purview という名前になっています。

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、ルート コレクションが強調表示されています。" border="true":::

すべての既存のコレクションは、このルート コレクションに追加されており、このページから管理できます。
新しいコレクションを作成するには、 **[+ コレクションの追加]** を選択します。

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="コレクション追加ボタンが強調表示された新しいコレクション ウィンドウを示す Purview Studio ウィンドウのスクリーンショット。" border="true":::

コレクションを編集するには、コレクションの詳細ページ、またはコレクションのドロップダウン メニューから、 **[編集]** を選択します。

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、選択したコレクション ウィンドウと、コレクションの名前の横にある省略記号ボタンの両方で、[編集] ボタンが強調表示されています。" border="true":::

コレクション内のロールの割り当てを編集するには、 **[ロールの割り当て]** タブを選択して、コレクション内のすべてのロールを表示します。 ロールの割り当てを管理できるのは、コレクション管理者のみです。 アップグレードされたアカウントのアクセス許可の詳細については、[以下のセクション](#permissions)を参照してください。

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="[ロールの割り当て] タブが強調表示されている Purview Studio コレクション ウィンドウのスクリーンショット。" border="true":::

アップグレードされたアカウントのコレクションの詳細については、[コレクションの作成と管理に関するガイド](how-to-create-and-manage-collections.md)を参照してください。

### <a name="what-happens-to-your-collections-during-upgrade"></a>アップグレード中のコレクションに対する処理

1. ルート コレクションが作成されます。 ルート コレクションは、コレクション リストの一番上のコレクションであり、Purview リソースと同じ名前になります。 次の例では、Contoso Purview という名前になっています。

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、ルート コレクションが強調表示されています。" border="true":::

1. 既存のコレクションはルート コレクションに結び付けられます。 それらのコレクションは、ルート コレクションの下に表示され、そこで操作できます。

### <a name="what-happens-to-your-sources-during-upgrade"></a>アップグレード中のソースに対する処理

1. コレクションにまだ関連付けられていなかったソースは、ルート コレクションに自動的に追加されます。

1. コレクションに既に関連付けられていたソースまたはスキャンは、アップグレードされたアカウントでそれぞれのコレクションに追加されます。

資産も、アップグレードされたアカウントのコレクションに関連付けられますが、アップグレード後のコレクションですぐに表示されない場合があります。 この現象は、次のいずれかの理由で発生します。

* この資産はスケジュールされたスキャンによってスキャンされたが、次のスキャン実行がまだ行われていない。
* この資産は、1 回限りのスキャンによってスキャンされた。

> [!NOTE]
> 資産はスキャン プロセス中にコレクションに追加されるため、新しいコレクションに資産を追加するにはもう一度資産をスキャンする必要があります。

スケジュールされたスキャンの場合、必要なのはスキャンが次に実行されるまで待つことだけです。次の実行で、資産がコレクションに追加されます。

1 回限りのスキャンの場合は、そのスキャンを手動で再実行して、コレクションに資産を追加する必要があります。

1. [Purview Studio](https://web.purview.azure.com/resource/) で [Data Map] を開き、 **[ソース]** を選択します。 スキャンするソースを選択します。

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、ソースが強調表示されています。" border="true":::

1. **[スキャン]** タブを選択し、再実行するスキャンを選択します。

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="Purview Studio ウィンドウのスクリーンショット。ソースが開かれ、スキャンが強調表示されています。" border="true":::

1. **[今すぐスキャンを実行する]** を選択して、スキャンを再実行します。

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="Purview Studio ウィンドウのスクリーンショット。スキャンが開かれ、[今すぐスキャンを実行する] が強調表示されています。" border="true":::

### <a name="what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin"></a>アップグレードしたアカウントにコレクション管理者がいない場合

プロセスが少なくとも 1 つのユーザーまたはグループを次の順序で識別できる場合、アップグレードされた Purview アカウントには既定のコレクション管理者がいます。 

1. 所有者 (明示的に割り当て)

1. ユーザー アクセス管理者 (明示的に割り当て)

1. データ ソース管理者およびデータ キュレーター

アカウントに上記の条件と一致するユーザーまたはグループがない場合、アップグレードされた Purview アカウントにはコレクション管理者が存在しません。 

管理 API を使用してコレクション管理者を手動で追加することはできます。 書き込みアクションを実行するには、この API を呼び出すユーザーが、Purview アカウントに対する所有者またはユーザー アクセス管理者の権限を持っている必要があります。 API 経由で送信する新しいコレクション管理者の `objectId` を把握しておく必要があります。

#### <a name="request"></a>Request

   ```
    POST https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Purview/accounts/<accountName>/addRootCollectionAdmin?api-version=2021-07-01
   ```    
    
#### <a name="request-body"></a>要求本文

   ```json
    {
        "objectId": "<objectId>"
    }
   ```    

`objectId` は、追加する新しいコレクション管理者の objectId です。

#### <a name="response-body"></a>応答本文

成功した場合は、`200` コードと共に空の本文の応答が返されます。

失敗した場合、応答本文の形式は次のとおりです。

   ```json
    {
        "error": {
            "code": "19000",
            "message": "The caller does not have Microsoft.Authorization/roleAssignments/write permission on resource: [/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>].",
            "target": null,
            "details": []
        }
    }
   ```

## <a name="permissions"></a>アクセス許可

アップグレードされた Purview アカウントでは、コレクションによってアクセス許可が管理されます。

> [!NOTE]
> アップグレードされたアカウントでは、アクセス許可が Access Control (IAM) によって管理されなくなります。 レガシ アカウントがアップグレードされると、既存の IAM アクセス許可は Access Control IAM の下に引き続き表示されますが、Purview でのアクセスに影響することはなくなります。

レガシ アカウントがアップグレードされると、Access Control IAM で割り当てられたすべてのアクセス許可は、ルート コレクションで、次のいずれかのロールに割り当てられます。

* **コレクション管理者** - コレクションとその詳細を編集し、コレクション内のアクセスを管理し、サブコレクションを追加できます。
* **データ ソース管理者** - データ ソースとデータ スキャンを管理できます。
* **データ キュレーター** - カタログ データ オブジェクトに対するアクションを作成、読み取り、変更、および削除できます。
* **データ リーダー** - カタログ データ オブジェクトにアクセスできますが、変更はできません。

Purview のソースと成果物へのアクセス権をユーザーに付与するために、それらのユーザーがアクセスする必要のあるデータが存在する、コレクションおよびサブコレクションへのアクセス権を付与することができます。

> [!NOTE]
> コレクション管理者だけが、コレクション内のアクセスを管理するためのアクセス許可を持っています。

コレクション内のロールの割り当てを表示または編集するには、コレクション内で **[ロールの割り当て]** タブを選択します。

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="[ロールの割り当て] タブが強調表示されている Purview Studio コレクション ウィンドウのスクリーンショット。" border="true":::

アップグレードされた Purview アカウントにおけるアクセス許可の管理の詳細については、[Purview のアクセス許可に関するガイド](catalog-permissions.md)を参照してください。

## <a name="next-steps"></a>次のステップ

上記の概念の詳細、および Purview リソースのセキュリティ保護の詳細については、以下のリンク先を参照してください。

* [Azure Purview でのプライベート エンドポイント](catalog-private-link.md)
* [エラスティック データ マップ](concept-elastic-data-map.md)
* [Purview のアクセス許可に関するガイド](catalog-permissions.md)

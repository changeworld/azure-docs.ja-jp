---
title: Azure Event Hubs スキーマ レジストリを作成する
description: この記事では、Azure Event Hubs 名前空間にスキーマ レジストリを作成する方法について説明します。
ms.topic: quickstart
ms.date: 06/01/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f15327c6e5f35dcdd37ee45222f351257c846f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070843"
---
# <a name="create-an-azure-event-hubs-schema-registry"></a>Azure Event Hubs スキーマ レジストリを作成する
この記事では、Azure Event Hubs によってホストされるスキーマ レジストリに、スキーマを含むスキーマ グループを作成する方法について説明します。 Azure Event Hubs のスキーマ レジストリ機能の概要については、「[Event Hubs の Azure スキーマ レジストリ](schema-registry-overview.md)」を参照してください。

> [!NOTE]
> - この機能は、**basic** レベルでは使用できません。
> - イベント ハブが **仮想ネットワーク** にある場合、同じ仮想ネットワーク内の VM からポータルにアクセスしない限り、Azure portal にスキーマを作成することはできません。 

## <a name="prerequisites"></a>前提条件
[Event Hubs 名前空間を作成します](event-hubs-create.md#create-an-event-hubs-namespace)。 既存の名前空間を使用することもできます。 

## <a name="create-a-schema-group"></a>スキーマ グループを作成する
1. **[Event Hubs 名前空間]** ページに移動します。 
1. 左側のメニューの **[スキーマ レジストリ]** を選択します。 スキーマ グループを作成するには、ツールバーの **[+ スキーマ グループ]** を選択します。 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Azure portal の [スキーマ レジストリ] ページが表示されているスクリーンショット。":::
1. **[スキーマ グループの作成]** ページで、次の手順を実行します。
    1. スキーマ グループの **名前** を入力します。
    1. **[シリアル化の種類]** については、スキーマ グループ内のすべてのスキーマに適用されるシリアル化形式を選択します。 現時点でサポートされている種類は **Avro** のみであるため、 **[Avro]** を選択します。 
    1. グループ内のすべてのスキーマに対して、**互換モード** を選択します。 **[Avro]** の場合、上位および下位の各互換性モードがサポートされています。 
    1. 次に、 **[作成]** を選択して、スキーマ グループを作成します。 
    
        :::image type="content" source="./media/create-schema-registry/create-schema-group-page.png" alt-text="スキーマ グループを作成するためのページが表示されている画像":::
1. スキーマ グループの一覧で、**スキーマ グループ** の名前を選択します。

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="選択した一覧にスキーマ グループが表示されている画像。":::    
1. グループの **[スキーマ グループ]** ページが表示されます。

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="[スキーマ グループ] ページが表示されている画像":::
    

## <a name="add-a-schema-to-the-schema-group"></a>スキーマ グループにスキーマを追加する
このセクションでは、Azure portal を使用してスキーマ グループにスキーマを追加します。 

1. **[スキーマ グループ]** ページで、ツールバーの **[+ スキーマ]** を選択します。 
1. **[スキーマの作成]** ページで、こちらの手順を実行します。
    1. **[名前]** に「**orderschema**」と入力します。
    1. 次の **スキーマ** をテキスト ボックスにコピーします。 スキーマを含むファイルを選択することもできます。
    
        ```json
        {
          "namespace": "com.azure.schemaregistry.samples",
          "type": "record",
          "name": "Order",
          "fields": [
            {
              "name": "id",
              "type": "string"
            },
            {
              "name": "amount",
              "type": "double"
            }
          ]
        }
        ```
    1. **［作成］** を選択します 
1. スキーマの一覧から **スキーマ** を選択します。 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="選択されたスキーマが表示されている画像。":::
1. スキーマの **[スキーマの概要]** ページが表示されます。 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="[スキーマの概要] ページが表示されている画像。":::    
1. 複数のバージョンのスキーマがある場合、これらは **[バージョン]** ドロップダウン リストに表示されます。 バージョンを選択して、そのバージョン スキーマに切り替えます。 

## <a name="create-a-new-version-of-schema"></a>新しいバージョンのスキーマを作成する

1. テキスト ボックス内のスキーマを更新し、 **[検証]** を選択します。 次の例では、新しいフィールド `description` がスキーマに追加されています。 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="[スキーマの更新] ページが表示されている画像":::    
    
1. 検証の状態と変更を確認し、 **[保存]** を選択します。 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="検証の状態、変更、保存が示された [レビュー] ページが表示されている画像":::     
1. **[スキーマの概要]** ページの **[バージョン]** で [`2`] が選択されていることがわかります。 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="スキーマの新しいバージョンが表示された画像":::    
1. `1` を選択し、スキーマのバージョン 1 を確認します。 


## <a name="next-steps"></a>次のステップ
スキーマレジストリの詳細については、「[Event Hubs の Azure スキーマ レジストリ](schema-registry-overview.md)」を参照してください。

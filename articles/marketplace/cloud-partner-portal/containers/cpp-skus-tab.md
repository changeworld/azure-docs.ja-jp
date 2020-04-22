---
title: Azure Containers イメージ用の SKU | Azure Marketplace
description: Azure コンテナーの SKU を構成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 9c5cf218632c720fd042cc5f5d4ed95d5096b5b5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270198"
---
# <a name="container-skus-tab"></a>コンテナーの SKU のタブ

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure Container オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure Container オファーを作成する](https://aka.ms/CreateContainerOffer)」の手順に従って、移行されたオファーを管理します。

**[新しいプラン]** ページの **[SKU]** タブを使用すると、1 つまたは複数の SKU を作成して、それらを新しいプランに関連付けることができるようになります。  異なる SKU を使用して、機能セット、課金モデル、その他の特性によってソリューションを区別できます。

## <a name="sku-settings"></a>SKU の設定

新しいプランの作成を開始したときは、プランに関連付けられている SKU が何もない状態です。 新しい SKU を作成するには、次の手順に従います。

1. [SKU] タブで、 **[新しい SKU]** を選択します

   ![新しい SKU のプロンプト](./media/containers-sku-settings.png)

2. 必須 SKU とコンテナーの情報を入力します。 各 SKU が 1 つのコンテナー イメージに対応します。 SKU は 2 つの部分に分かれています。

    -   SKU メタデータ
    -   コンテナー メタデータ


### <a name="sku-metadata"></a>SKU メタデータ

SKU メタデータには、コンテナー一覧のネットショップの表示情報が含まれています。

![SKU メタデータ](./media/containers-sku-details.png)


### <a name="container-metadata"></a>コンテナー メタデータ

コンテナー メタデータには、Azure Container Registry (ACR) 内にあるイメージ リポジトリの詳細の参照情報が含まれています。 Azure Marketplace によってこのイメージが Marketplace 固有のパブリック レジストリにコピーされ、そのイメージは認定後に顧客向けに利用可能になります。 Azure ユーザーからのすべての Azure Marketplace コンテナー イメージ使用要求は、ACR ではなく Marketplace のパブリック レジストリから処理されます。

![コンテナー メタデータ](./media/containers-image-repository.png)
    
上記のスクリーン キャプチャに示されている**イメージ リポジトリの詳細**には、次のフィールドがあります。  必須フィールドはアスタリスク (*) で示されます。

-   **[サブスクリプション ID]\*** - ACR が存在する Azure サブスクリプション ID です。
-   **[リソース グループ名]\*** - ACR のリソース グループ名です。
-   **[レジストリ名]\*** - ACR の名前です。
-   **[リポジトリ名]\*** – リポジトリ名です。 この名前を設定した後で、この値を変更することはできません。 アカウント内の他のオファーとの競合を回避するには、一意の名前を使用します。
-   **[ユーザー名]\*** - ACR イメージに関連付けられているユーザー名 (管理者ユーザー名) です。
-   **[パスワード]\*** - ACR イメージに関連付けられているパスワードです。

    >[!NOTE]
    >発行プロセスで説明した ACR にパートナーが確実にアクセスできるようにするため、ユーザー名とパスワードが必要です。


### <a name="image-version"></a>イメージ バージョン

コンテナー イメージを発行する際、1 つ以上のイメージ タグおよび SHA ダイジェストを指定することもできます。

**イメージ タグ\*またはダイジェスト**
 
- このタグまたはダイジェストには、`latest` タグとバージョン タグを含める必要があります (たとえば、先頭が `xx.xx.xx-` (xx は数字))。 複数のプラットフォームを対象とするには、[マニフェスト タグ](https://github.com/estesp/manifest-tool)にする必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。 
- タグを使用して、コンテナーの複数のバージョンを追加できます。 `latest` を除くすべてのマニフェスト タグは、`X.Y-` または `X.Y.Z-` (X、Y、Zは整数) で始まる必要があります。 <br/> たとえば、`latest` タグが `1.0.1-linux-x64`、`1.0.1-linux-arm32`、`1.0.1-windows-arm32` を指している場合、これらのタグをここに追加する必要があります。

>[!NOTE]
>テスト中にイメージを識別できるようにするため、必ず**テスト タグ**をイメージに追加してください。


## <a name="next-steps"></a>次のステップ

[[Marketplace] タブ](./cpp-marketplace-tab.md)を使用して、プランのためのマーケットプレースの説明を作成します。 

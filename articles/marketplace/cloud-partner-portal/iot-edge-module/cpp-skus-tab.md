---
title: Azure IoT Edge モジュール SKU | Azure Marketplace
description: IoT Edge モジュールの SKU を作成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: c1d1daea71ea8b69ef24031c643caf8dcd0d1fb7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983332"
---
# <a name="iot-edge-module-skus-tab"></a>[IoT Edge モジュール SKU] タブ

>[!Important]
>2020 年 4 月 13 日より、IoT Edge モジュール オファーの管理はパートナー センターに移行されます。 移行後、オファーはパートナー センターで作成および管理されます。 移行後のオファーは、「[IoT Edge モジュール オファーの作成](https://aka.ms/AzureCreateIoT)」の手順に従って管理します。

**[新しいプラン]** ページの **[SKU]** タブを使用すると、1 つまたは複数の SKU を作成して、それらを新しいプランに関連付けることができるようになります。  異なる SKU を使用して、機能セット、課金モデル、その他のいくつかの特性によってソリューションを区別できます。


## <a name="sku-settings"></a>SKU の設定

新しいプランの作成を開始したときは、プランに関連付けられている SKU が何もない状態です。 新しい SKU を作成するには、次の手順に従います。

- **[IoT Edge モジュール] > [新しいプラン]** ページで、 **[SKU]** タブを選択します。
- [SKU] の下で **[+ 新しい SKU]** を選択し、ダイアログ ボックスを開きます。

  ![IoT Edge モジュールの [新しいプラン] タブの [新しい SKU] ボタン](./media/iot-edge-module-skus-tab-new-sku.png)

- **[新しい SKU]** ダイアログ ボックスで SKU の ID を入力し、 **[OK]** を選択します
(次の表に、ID の命名規則を示します)。

**[SKU]** タブが更新され、SKU を構成するために編集したフィールドが表示されます。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU ID\***       | この SKU の識別子。 この名前は、最大 50 文字で、小文字の英数字またはダッシュ (-) を使用しますが、末尾にダッシュを使用することはできません。 **注:** プランの発行後は、この名前を変更することはできません。 名前は、製品 URL に公開表示されます。 |
|  |  |


## <a name="sku-details"></a>SKU の詳細

Azure Marketplace および Azure portal の Web サイトでの SKU の表示方法を定義するには、 **[SKU Details] (SKU の詳細)** を構成します。

![IoT Edge モジュールの SKU のメタデータ](media/iot-edge-module-skus-tab-metadata.png)

次の表で、 **[SKU Details] (SKU の詳細)** 下のフィールドの目的、内容、書式設定について説明します。 必須フィールドはアスタリスク (*) で示されます。

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **タイトル\***        | 当該 SKU のタイトル。 最大長は 50 文字です。 <br/> これは Azure portal に表示され、デプロイ時に既定のモジュール名 (スペースや特殊文字は含まない) として使用されます。 当該フィールドが表示される場所は、次の図を参照してください。|
| **要約\***      | 当該 SKU の概要。 最大長は 100 文字です。 プランは**要約せず**、SKU だけを要約してください。  この概要は、Azure Marketplace で表示されます。 当該フィールドが表示される場所は、次の図を参照してください。|
| **説明\***  | 当該 SKU の簡単な説明。最大長は 3000 文字です。 プランは説明せず、SKU だけを説明してください。 この項目は Azure Marketplace および Azure portal で表示されます。 Azure portal では、[Marketplace] タブで定義されているプランを記述する Marketplace の説明に追加されます。SKU の概要と同じにすることができます。 当該フィールドが表示される場所は、次の図を参照してください。|
| **この SKU を非表示にする\*** | 既定の設定の、**No** のままにします。 |
|  |  |


### <a name="sku-example"></a>SKU の例

 次の例は、SKU の **[タイトル]** 、 **[概要]** 、 **[説明]** フィールドが、さまざまなビューでどのように表示されるかを示しています。
 

#### <a name="on-the-azure-marketplace-website"></a>Azure Marketplace Web サイト:

- SKU の詳細を表示する場合:

    ![Azure Marketplace Web サイトでの SKU の表示](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Azure portal Web サイト:

- SKU を参照する場合:

    ![Azure portal の参照時の IoT Edge モジュールの表示 #1](media/iot-edge-module-portal-browse.png)

    ![Azure portal の参照時の IoT Edge モジュールの表示 #2](media/iot-edge-module-portal-product-picker.png)

- SKU を検索する場合:

    ![Azure portal の検索時の IoT Edge モジュールの表示](media/iot-edge-module-portal-search.png)

- SKU の詳細を表示する場合:

    ![ポータルで製品の詳細を確認するときに、IoT Edge モジュールがどのように表示されるか](./media/iot-edge-module-portal-pdp.png)

- モジュールをデプロイする場合:
    
    ![IoT Edge モジュールをデプロイするときの表示](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU の内容

**Edge モジュールのイメージ**の下に、IoT Edge モジュールをアップロードするために必要な情報を指定します。

IoT Edge モジュールのイメージを含む [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) へのアクセスを可能にし、それにより、アップロードして認証できるようにします。 発行後、IoT Edge モジュールがコピーされ、Azure Marketplace でホストされているパブリック コンテナー レジストリを使用して配布されます。

タグを使用して、複数のプラットフォームを対象にし、複数のバージョンを提供できます。 [タグとバージョン管理の詳細については、「Prepare your IoT Edge module technical assets (IoT Edge モジュールの技術アセットの準備)」](./cpp-create-technical-assets.md)を参照してください。

![IoT Edge モジュール イメージ](./media/iot-edge-module-skus-tab-acr.png)

次の表で、 **[Image Repository Details]\(イメージ リポジトリの詳細\)** および **[イメージ バージョン]** の各セクションのフィールドの目的、内容、書式設定について説明します。  必須フィールドはアスタリスク (*) で示されます。


|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***イメージ リポジトリの詳細***    |
| **サブスクリプション ID\***        | ACR の Azure サブスクリプション ID。|
| **リソース グループ名\***      | ACR のリソース グループの名前。|
| **レジストリ名\***  | ACR レジストリ名。 ログイン サーバー名ではなくレジストリ名のみコピーしてください (たとえば、`azurecr.io` を含めない)。 |
| **リポジトリ名\***  | IoT Edge モジュールを含む ACR のリポジトリ名。 **注:** 名前を設定すると、後で変更することはできません。 一意の名前を使用して、お使いのアカウントのその他のプランと同じ名前にならないようにしてください。 |
| **ユーザー名\*** | ACR に関連付けられているユーザー名 (管理者ユーザー名)。 |
| **パスワード\*** | ACR に関連付けられているパスワード。 |
|    |  ***イメージ バージョン***   |
| **Image Tag or Digest (イメージ タグまたはダイジェスト)\*** | 少なくとも `latest` タグとバージョン タグを含める必要があります (たとえば、先頭が `xx.xx.xx-` (xx は数字))。 複数のプラットフォームを対象とするには、[マニフェスト タグ](https://github.com/estesp/manifest-tool)にする必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。 タグを使用して、IoT Edge モジュールの複数のバージョンを追加できます。 `latest` を除くすべてのマニフェスト タグは、`X.Y-` または `X.Y.Z-` (X、Y、Zは整数) で始まる必要があります。 [タグとバージョン管理の詳細については、「Prepare your IoT Edge module technical assets (IoT Edge モジュールの技術アセットの準備)」](./cpp-create-technical-assets.md)を参照してください。 <br/> たとえば、`latest` タグが `1.0.1-linux-x64`、`1.0.1-linux-arm32`、`1.0.1-windows-arm32` を指すタグを指している場合、これらの 6 つのタグをここに追加する必要があります。 |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>お客様が既定の設定を使用して起動できるようにする

IoT Edge モジュールをデプロイするための最も一般的な設定を定義します。 こうした既定設定によって IoT Edge モジュールをすぐに起動できるようにし、お客様のデプロイを最適化します。

![デプロイ時の IoT Edge モジュールの既定設定](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

次の表で、**既定のルート**、**既定ツインの目的のプロパティ**、**既定の環境変数**、**既定の CreateOptions** の各フィールドの目的、内容、書式設定について説明します。

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **既定のルート**        | それぞれの既定のルート名と値は 512 文字未満でなければなりません。 最大 5 つの既定のルートを定義できます。 ルート値には正しい[ルート構文](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)を使用してください。 モジュールを参照するには、既定のモジュール名を使用します。モジュール名は、スペースと特殊文字を除いた **SKU のタイトル**です。 未知の他のモジュールを参照するには、お客様がこの情報を更新する必要があることが分かるように `<FROM_MODULE_NAME>` 規約を使用します。 詳細については、[IoT Edge のルート](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)に関するページをご覧ください。 <br/> たとえば、モジュール `ContosoModule` が `ContosoInput` の入力をリッスンし、`ContosoOutput` の出力データをリッスンする場合、次の 2 つの既定のルートを定義するのが適切です。<br/>- 名前 #1: `ToContosoModule`<br/>- 値 #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- 名前 #2: `FromContosoModuleToCloud`<br/>- 値 #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **既定ツインの目的のプロパティ**      | それぞれの既定ツインの目的のプロパティ名と値は 512 文字未満でなければなりません。 最大 5 つの名前/値のツインの目的のプロパティを定義できます。 ツインの目的のプロパティの値は、有効な JSON (エスケープされていない) で、配列を含まず、最大ネスト階層は 4 でなければなりません。 [ツインの目的のプロパティ](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)について詳細をご確認ください。 <br/> たとえば、モジュールがツインの目的のプロパティによって動的に構成可能なリフレッシュ レートをサポートする場合、以下の既定ツインの目的のプロパティを定義するのが適切です。<br/> - 名前 #1: `RefreshRate`<br/>- 値 #1: `60`|
| **既定の環境変数**  | それぞれの既定の環境変数名と値は 512 文字未満でなければなりません。 最大 5 つの名前/値の環境変数を定義できます。 <br/>たとえば、モジュールが開始される前に使用条件に同意する必要がある場合は、次の環境変数を定義できます。<br/> - 名前 #1: `ACCEPT_EULA`<br/>- 値 #1: `Y`|
| **既定の createOptions**  | createOptions は 512 文字未満でなければなりません。 この項目は、有効な JSON (エスケープされていない) である必要があります。 [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules) について詳細をご確認ください。 <br/> たとえば、モジュールでポートをバインドする必要がある場合、次の createOptions を定義できます。<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

**[保存]** を選択して SKU 設定を保存します。 


## <a name="next-steps"></a>次のステップ

[[Marketplace] タブ](./cpp-marketplace-tab.md)を使用して、プランのためのマーケットプレースの説明を作成します。

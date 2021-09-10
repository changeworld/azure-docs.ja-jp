---
title: App Configuration ストアを別のリージョンに移動する
description: App Configuration ストアを異なるリージョンに移動する方法について学習します。
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: c3ffe773e16eb7a658f219e980711eca2add1aac
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077320"
---
# <a name="move-an-app-configuration-store-to-another-region"></a>App Configuration ストアを別のリージョンに移動する 

App Configuration ストアはリージョン固有のものであり、自動的にリージョン間で移動することはできません。 ターゲット リージョンに新しい App Configuration ストアを作成してから、ソース ストアから新しいターゲット ストアにコンテンツを移動する必要があります。 さまざまな理由により、構成を別のリージョンに移動する場合があります。 たとえば、可用性ゾーンがサポートされている新しい Azure リージョンを利用するため、特定のリージョンでのみ利用可能な機能やサービスをデプロイするため、あるいは内部ポリシーとガバナンスの要件を満たすためです。 

次の手順では、新しいターゲット ストアを作成し、現在のストアを新しいリージョンにエクスポートするプロセスについて説明します。 

## <a name="design-considerations"></a>設計上の考慮事項

始める前に、次の概念に注意してください。

* 構成ストア名はグローバルに一意です。 
* 新しい構成ストア内に、アクセス ポリシーとネットワーク構成設定を再構成する必要があります。

## <a name="create-the-target-configuration-store"></a>ターゲット構成ストアを作成する

### <a name="portal"></a>[ポータル](#tab/portal)     
ポータルで新しい App Configuration ストアを作成するには、これらの手順に従います。 
1.  [Azure portal](https://portal.azure.com) にサインインします。 ホーム ページの左上にある **[+ リソースの作成]** を選択します。 **[Marketplace を検索]** ボックスに「*App Configuration*」と入力し、<kbd>Enter</kbd> キーを押します。 

    ![App Configuration を検索する](../../includes/media/azure-app-configuration-create/azure-portal-search.png)
1. 検索結果の **[アプリ構成]** を選択し、 **[作成]** を選択します。

    ![[作成] を選択します](../../includes/media/azure-app-configuration-create/azure-portal-app-configuration-create.png)
1. **[Create App Configuration]\(App Configuration の作成\)** ペインで、次の設定を入力します。
    
    | 設定 | 推奨値 | 説明 |
    |---|---|---|
    | **サブスクリプション** | 該当するサブスクリプション | 元のストアの Azure サブスクリプションを選択します |
    | **リソース グループ** | 該当するリソース グループ | 元のストアの Azure リソース グループを選択します |
    | **リソース名** | グローバルに一意の名前 | ターゲットの App Configuration ストアに使用する一意のリソース名を入力します。 これを前の構成ストアと同じ名前にすることはできません。 |
    | **場所** | ターゲットの場所 | 構成ストアの移動先となるターゲット リージョンを選択します。 |
    | **価格レベル** | *Standard* | 目的の価格レベルを選択します。 詳細については、[App Configuration の価格のページ](https://azure.microsoft.com/pricing/details/app-configuration)を参照してください。 |
1. **[確認および作成]** を選択して設定を検証します。
1. **作成** を選択します。 デプロイには数分かかることがあります。
1. リソースがデプロイされたら、ソース ストアのアクセス ポリシーとネットワーク構成設定を再作成します。 これらは構成と一緒に転送されません。 これには、ID、仮想ネットワーク、パブリック ネットワーク アクセスの管理の使用が含まれる場合があります。 
    
#### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
CLI で新しい App Configuration ストアを作成するには、これらの手順に従います。 
1. 資格情報を使用して、Azure CLI にログインします。
    ```azurecli
    az login
    ```
1. `create` コマンドを使用して、新しい構成ストアを作成します。
    ```azurecli
    az appconfig create -g MyResourceGroup -n MyResourceName -l targetlocation --sku Standard 
    ```
    その後、次の設定を入力します。

    | 設定 | 推奨値 | 説明 |
    |---|---|---|
    | **リソース グループ** | 該当するリソース グループ | 元のストアの Azure リソース グループを選択します |
    | **リソース名** | グローバルに一意の名前 | ターゲットの App Configuration ストアに使用する一意のリソース名を入力します。 これを前の構成ストアと同じ名前にすることはできません。 |
    | **場所** | ターゲットの場所 | 構成ストアの移動先となるターゲット リージョンを選択します。 |
    | **SKU** | *Standard* | 目的の価格レベルを選択します。 詳細については、[App Configuration の価格のページ](https://azure.microsoft.com/pricing/details/app-configuration)を参照してください。 |
1. デプロイには数分かかることがあります。 完了したら、ソース ストアのアクセス ポリシーとネットワーク構成設定を再作成します。 これらは構成値と一緒に転送されません。 これには、ID、仮想ネットワーク、パブリック ネットワーク アクセスの管理の使用が含まれる場合があります。 詳細については、[CLI のドキュメント](./cli-samples.md)を参照してください。
---

## <a name="transfer-your-configuration-key-values"></a>構成のキー値を転送する  

### <a name="portal"></a>[ポータル](#tab/portal)
ポータルを使用して構成をターゲット ストアにエクスポートするには、これらの手順に従います。
1. [Azure portal](https://portal.azure.com) でご利用のソース構成ストアに移動し、 **[操作]** で **[インポート/エクスポート]** を選択します。
1. **[エクスポート]** を選択し、 **[ターゲット サービス]** ドロップダウンで **[App Configuration]** を選びます。 
    ![別の構成ストアにエクスポートする](media/export-to-config-store.png)
1. **[リソースの選択]** をクリックし、**サブスクリプション** と **リソース グループ** を入力します。 **リソース** は、前に作成したターゲット構成ストアの名前です。 
1. **[適用]** を選択して、ターゲット構成ストアを確認します。 
1. from ラベル、time、および Label フィールドは既定値のままにし、 **[適用]** を選択します。 
1. 構成がソースからターゲット ストアに正常に転送されたことを確認するには、ポータルでターゲット構成ストアに移動します。 **[操作]** で **[構成エクスプローラー]** を選択し、これに元のストアのものと同じキーと値のペアが含まれていることを確認します。 
    > [!NOTE]
    > このプロセスでは、構成キー値を一度に 1 つのラベルでのみエクスポートできます。 複数をエクスポートするには、ラベルごとに手順 2 から 5 を繰り返します。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
Azure を使用してターゲット ストアに構成をエクスポートするには、これらの手順に従います。
1. Azure CLI で、ソース構成ストアのすべての値をターゲット構成ストアにエクスポートする次のコマンドを入力します。 
    ```azurecli
    az appconfig kv export -n SourceConfigurationStore -d appconfig --dest-name TargetConfigurationStore --key * --label * --preserve-labels
    ```
1. ソースからターゲット ストアに構成が正常に転送されたことを確認するには、ターゲット ストア内のすべてのキー値を一覧表示します。 
    ```azurecli
    az appconfig kv list -n TargetAppConfiguration --all
    ```
---
## <a name="delete-your-source-configuration-store"></a>ソース構成ストアを削除する 

構成がターゲット ストアに転送されている場合は、ソース構成ストアを削除するように選択できます。 

### <a name="portal"></a>[ポータル](#tab/portal)
ポータルでソース構成ストアを削除するには、これらの手順に従います。
1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。
1. **[名前でフィルター]** ボックスにリソース グループの名前を入力します。 
1. 結果一覧でリソース グループ名を選択し、概要を表示します。
1. ソース構成ストアを選び、 **[概要]** ブレードで **[削除]** を選択します。 
1. 構成ストアの削除の確認を求められたら、 **[はい]** を選択します。

しばらくすると、ソース構成ストアが削除されます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
Azure CLI でソース構成ストアを削除するには、これらの手順に従います。
1. Azure CLI で、次のコマンドを実行します。 
    ```azurecli
    az appconfig delete -g ResourceGroupName -n SourceConfiguration
    ```
    **リソース グループ** は、ソース構成ストアに関連付けられているものであることに注意してください。 
1. ソース構成ストアの削除には、しばらく時間がかかることがあります。 リソース グループ内の現在のすべての構成ストアを一覧表示することで、操作が成功したことを確認できます。 
    ```azurecli
    az appconfig list -g MyResourceGroup
    ```
    しばらくすると、ソース構成ストアが削除されます。

---
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure App Configuration ストアからキー値を自動的にバックアップする](./howto-move-resource-between-regions.md)
>[Azure App Configuration の回復性とディザスター リカバリー](./concept-disaster-recovery.md)

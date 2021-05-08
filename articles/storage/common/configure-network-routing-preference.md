---
title: ネットワーク ルーティング設定の構成
titleSuffix: Azure Storage
description: Azure ストレージ アカウントに対してネットワークのルーティング優先設定を構成し、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定します。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790457"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Azure Storage に対してネットワークのルーティング優先設定を構成する

この記事では、ストレージ アカウントに対して、ネットワークのルーティング優先設定とルート固有のエンドポイントを構成する方法について説明します。 

ネットワークのルーティング優先設定では、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定します。 ルート固有のエンドポイントは、Azure Storage によってお使いのストレージ アカウント用に作成される新しいエンドポイントです。 これらのエンドポイントでは、既定のルーティング優先設定を変更せずに、目的のパス経由でトラフィックがルーティングされます。 詳細については、「[Azure Storage のネットワーク ルーティング優先設定](network-routing-preference.md)」を参照してください。

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>既定のパブリック エンドポイントのルーティング優先設定を構成する

既定では、ストレージ アカウントのパブリック エンドポイントのルーティング優先設定は、Microsoft グローバル ネットワークに設定されています。 ストレージ アカウントのパブリック エンドポイントに対する既定のルーティング優先設定として、Microsoft グローバル ネットワーク ルーティングとインターネット ルーティングのどちらかを選択できます。 これら 2 種類のルーティングの違いについて詳しくは、「[Azure Storage のネットワーク ルーティング優先設定](network-routing-preference.md)」を参照してください。 

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ルーティング優先設定をインターネット ルーティングに変更するには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルでストレージ アカウントに移動します。

3. **[設定]** で、 **[ネットワーク]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[ネットワーク] メニュー オプション](./media/configure-network-routing-preference/networking-option.png)

4.  **[ファイアウォールと仮想ネットワーク]** タブの **[ネットワーク ルーティング]** で、 **[ルーティングの優先順位]** 設定を **[Internet routing]\(インターネット ルーティング\)** に変更します。

5.  **[保存]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![インターネット ルーティング オプション](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. `Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って認証を行います。

   ```powershell
   Connect-AzAccount
   ```

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. ルーティングの設定をインターネット ルーティングに変更するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを使用して、`--routing-choice` パラメーターを `InternetRouting` に設定します。

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   `<resource-group-name>` プレースホルダーの値を、ストレージ アカウントが含まれているリソース グループの名前に置き換えます。

   `<storage-account-name>` プレースホルダーの値を、ストレージ アカウントの名前に置き換えます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure サブスクリプションにサインインします。

   - Azure Cloud Shell を起動するには、[Azure portal](https://portal.azure.com) にサインインします。

   - CLI のローカル インストールにログインするには、[az login](/cli/azure/reference-index#az_login) コマンドを実行します。

     ```azurecli
     az login
     ```
2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. ルーティングの設定をインターネット ルーティングに変更するには、[az storage account update](/cli/azure/storage/account#az_storage_account_update) コマンドを使用して、`--routing-choice` パラメーターを `InternetRouting` に設定します。

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

---

## <a name="configure-a-route-specific-endpoint"></a>ルート固有のエンドポイントを構成する

ルート固有のエンドポイントを構成することもできます。 たとえば、既定のエンドポイントのルーティング優先設定を "*インターネット ルーティング*" に設定してから、インターネット上のクライアントとストレージ アカウントとの間のトラフィックを Microsoft グローバル ネットワーク経由でルーティングできるようにする、ルート固有のエンドポイントを公開できます。

この優先設定は、ルート固有のエンドポイントにのみ影響します。 この優先設定は、既定のルーティング優先設定には影響しません。  

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1.  ポータルでストレージ アカウントに移動します。

2.  **[設定]** で、 **[ネットワーク]** を選択します。

3.  **[ファイアウォールと仮想ネットワーク]** タブの **[Publish route-specific endpoints]\(ルート固有のエンドポイントを公開する\)** の下で、ルート固有のエンドポイントのルーティング優先設定を選択し、 **[保存]** をクリックします。

    次の図は、選択された **[Microsoft network routing]\(Microsoft ネットワーク ルーティング\)** オプションを示しています。

    > [!div class="mx-imgBorder"]
    > ![[Microsoft network routing]\(Microsoft ネットワーク ルーティング\) オプション](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. ルート固有のエンドポイントを構成するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを使用します。 

   - Microsoft ネットワーク ルーティング設定を使用するルート固有のエンドポイントを作成するには、`-PublishMicrosoftEndpoint` パラメーターを `true` に設定します。 

   - インターネット ルーティング設定を使用するルート固有のエンドポイントを作成するには、`-PublishInternetEndpointTo` パラメーターを `true` に設定します。  

   次の例では、Microsoft ネットワーク ルーティングの設定を使用するルート固有のエンドポイントを作成します。

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   `<resource-group-name>` プレースホルダーの値を、ストレージ アカウントが含まれているリソース グループの名前に置き換えます。

   `<storage-account-name>` プレースホルダーの値を、ストレージ アカウントの名前に置き換えます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. ルート固有のエンドポイントを構成するには、[az storage account update](/azure/storage/account#az_storage_account_update) コマンドを使用します。 

   - Microsoft ネットワーク ルーティング設定を使用するルート固有のエンドポイントを作成するには、`--publish-microsoft-endpoints` パラメーターを `true` に設定します。 

   - インターネット ルーティング設定を使用するルート固有のエンドポイントを作成するには、`--publish-internet-endpoints` パラメーターを `true` に設定します。  

   次の例では、Microsoft ネットワーク ルーティングの設定を使用するルート固有のエンドポイントを作成します。

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   `<storage-account-name>` プレースホルダーの値を、ストレージ アカウントの名前に置き換えます。

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>ルート固有のエンドポイントのエンドポイント名を確認する

ルート固有のエンドポイントを構成した場合は、ストレージ アカウントのプロパティでそのエンドポイントを確認することができます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1.  **[設定]** の **[プロパティ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[プロパティ] メニュー オプション](./media/configure-network-routing-preference/properties.png)

2.  ルーティング優先設定がサポートされているサービスごとに、**Microsoft ネットワーク ルーティング** エンドポイントが表示されます。 次の図は、BLOB とファイル サービスのエンドポイントを示しています。

    > [!div class="mx-imgBorder"]
    > ![ルート固有のエンドポイントの Microsoft ネットワーク ルーティング オプション](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. エンドポイントをコンソールに出力するには、ストレージ アカウント オブジェクトの `PrimaryEndpoints` プロパティを使用します。

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   `<resource-group-name>` プレースホルダーの値を、ストレージ アカウントが含まれているリソース グループの名前に置き換えます。

   `<storage-account-name>` プレースホルダーの値を、ストレージ アカウントの名前に置き換えます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. エンドポイントをコンソールに出力するには、ストレージ アカウント オブジェクトの [az storage account show](/cli/azure/storage/account#az_storage_account_show) プロパティを使用します。

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   `<resource-group-name>` プレースホルダーの値を、ストレージ アカウントが含まれているリソース グループの名前に置き換えます。

   `<storage-account-name>` プレースホルダーの値を、ストレージ アカウントの名前に置き換えます。

---

## <a name="see-also"></a>関連項目

- [ネットワーク ルーティング設定](network-routing-preference.md)
- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](storage-network-security.md)
- [BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)

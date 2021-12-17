---
title: 'チュートリアル: Azure Import/Export を使用して Azure Blob Storage からデータをエクスポートする | Microsoft Docs'
description: Azure portal でエクスポート ジョブを作成して、Azure BLOB からデータを転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: ccda14f4046efe32370b206577807dca558d9f25
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709638"
---
# <a name="tutorial-export-data-from-azure-blob-storage-with-azure-importexport"></a>チュートリアル: Azure Import/Export を使用して Azure Blob Storage からデータをエクスポートする

この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Blob Storage から安全にエクスポートする手順について説明します。 このサービスを利用するには、Azure データセンターに空のドライブを送付する必要があります。 このサービスでは、ストレージ アカウントからドライブにデータをエクスポートし、ドライブを返送します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Import/Export を使用して Azure Blob Storage からデータをエクスポートするための前提条件
> * 手順 1:エクスポート ジョブの作成
> * 手順 2:ドライブを送付する
> * 手順 3:追跡情報を使用してジョブを更新する
> * 手順 4:ディスクを受け取る
> * 手順 5:ディスクのロックを解除する

## <a name="prerequisites"></a>前提条件

Azure Blob Storage からデータを転送するエクスポート ジョブを作成する前に、このサービスの次の前提条件の一覧を慎重に確認してください。
次の手順が必要です。

- Import/Export サービスに使用できるアクティブな Azure サブスクリプションがある。
- Azure Storage アカウントが少なくとも 1 つある。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)」を参照してください。
- 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。 Azure Import/Export ツールを使用して、提供するディスクの数を特定できます。 手順については、[使用するドライブ数の特定](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need)に関するページを参照してください。
- FedEx または DHL のアカウントを用意します。 FedEx または DHL 以外の運送業者を使用する場合、`adbops@microsoft.com` から Azure Data Box Operations チームまでお問い合わせください。
  - アカウントは、有効で、残高があり、差出人住所の機能を持っている必要があります。
  - エクスポート ジョブの追跡番号を生成します。
  - すべてのジョブに個別の追跡番号が必要です。 同じ追跡番号を持つ複数のジョブはサポートされていません。
  - 運送業者アカウントがいない場合、次に移動します。
    - [FedEx アカウントを作成するか](https://www.fedex.com/en-us/create-account.html)、または
    - [DHL アカウントを作成します](http://www.dhl-usa.com/en/express/shipping/open_account.html)。

## <a name="step-1-create-an-export-job"></a>手順 1:エクスポート ジョブの作成

### <a name="portal"></a>[ポータル](#tab/azure-portal)

以下の手順を実行して、Azure portal でエクスポート ジョブを作成します。

1. <https://portal.azure.com/> にログオンします。
2. **インポート/エクスポート ジョブ** を検索します。

    ![インポート/エクスポート ジョブを検索する](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **[+新規]** を選択します。

    ![[+新規] を選択して新しく作成する ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **[基本]** で次のようにします。

   1. サブスクリプションを選択します。
   1. リソース グループを選択するか、 **[新規作成]** を選択して新しく作成します。
   1. インポート ジョブのわかりやすい名前を入力します。 この名前は、ジョブの進行状況の追跡に使用します。
       * 名前には小文字、数字、ハイフンのみを含めることができます。
       * 名前はアルファベットから始める必要があります。スペースを含めることはできません。

   1. **[Azure からエクスポート]** を選択します。

    ![エクスポート注文の [基本] オプション](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    **[次へ: ジョブの詳細 >]** を選択して続行します。

5. **[ジョブの詳細]** で次の操作を実行します。

   1. データが現在存在する Azure リージョンを選択します。
   1. エクスポートするデータがあるストレージ アカウントを選択します。 所在地に近いストレージ アカウントを使用します。

      配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。

   1. ストレージ アカウントから空のドライブにエクスポートする BLOB データを指定します。 以下の 3 つの方法のいずれかを選択します。

      - ストレージ アカウントの BLOB データについて **[すべてをエクスポートする]** を選択します。

        ![[すべてをエクスポートする]](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - **[選択されているコンテナーと BLOB]** を選択し、エクスポートするコンテナーと BLOB を指定します。 複数の選択方法を使用できます。 **[追加]** オプションを選択すると右側のパネルが開き、選択文字列を追加できます。

        |オプション|説明|
        |------|-----------|      
        |**コンテナーの追加**|コンテナー内のすべての BLOB をエクスポートします。<br>**[コンテナーの追加]** を選択し、各コンテナーの名前を入力します。|
        |**BLOB の追加**|エクスポートする個々の BLOB を指定します。<br>**[BLOB の追加]** を選択します。 その後、コンテナー名から始まる BLOB への相対パスを指定します。 ルート コンテナーを指定するには、" *$root* " を使用します。<br>次のスクリーンショットで示すように、処理中にエラーが発生しないように、有効な形式で BLOB のパスを指定する必要があります。 詳細については、「[有効な BLOB パスの例](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths)」を参照してください。|
        |**プレフィックスの追加**|似た名前のコンテナーやコンテナー内の似た名前の BLOB のセットを選択するには、プレフィックスを使用します。 プレフィックスには、コンテナー名のプレフィックス、コンテナー名全体、またはコンテナー名全体の後に BLOB 名のプレフィックスを付けた名前を指定できます。 |

        ![選択したコンテナーと BLOB をエクスポートする](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - **[BLOB リスト ファイルからエクスポートする (XML 形式)]** を選択し、ストレージ アカウントからエクスポートする BLOB のパスとプレフィックスの一覧を含む XML ファイルを選択します。 XML ファイルを作成し、ストレージ アカウントのコンテナーに格納する必要があります。 このファイルは空にできません。

      > [!IMPORTANT]
      > XML ファイルを使用してエクスポートする BLOB を選択する場合は、XML に有効なパスまたはプレフィックスが含まれていることを確認してください。 ファイルが無効な場合、または指定されたパスと一致するデータがない場合は、一部のデータまたはすべてのデータがエクスポートされずに終了します。

       XML ファイルをコンテナーに追加する方法については、「[XML ファイルを使用した注文のエクスポート](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)」を参照してください。

      ![BLOB リスト ファイルからエクスポートする](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスによって BLOB のスナップショットが作成され、そのスナップショットがコピーされます。

   **[次へ: 出荷 >]** を選択して続行します。

6. **[出荷]** で、次の手順に従います。

    - ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 `adbops@microsoft.com` で Azure Data Box Operations チームに使用する予定の運送業者に関する情報を連絡してください。
    - その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、エクスポート ジョブの完了後、このアカウントを使ってドライブを返送します。
    - 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。

        > [!TIP]
        > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

    **[確認と作成]** を選択して続行します。

7. **[確認と作成]** では、以下を行います。

   1. ジョブの詳細を確認します。
   1. ジョブ名と、Azure にディスクを送付するために使用する Azure データセンターの送付先住所をメモします。

      > [!NOTE]
      > ディスクは常に、Azure Portal に示されているデータ センターに送付します。 誤って別のデータ センターにディスクが発送された場合、ジョブは処理されません。

   1. プライバシーとソース データの削除に関する注文の **使用条件** を確認します。 使用条件に同意する場合は、使用条件の下にあるチェック ボックスをオンにします。 注文の検証が開始されます。

   ![エクスポート注文を確認して作成する](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. 検証に合格した後、 **[作成]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

以下の手順を使用して、Azure portal でエクスポート ジョブを作成します。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>ジョブの作成

1. [az extension add](/cli/azure/extension#az_extension_add) コマンドを使用して、[az import-export](/cli/azure/import-export) 拡張機能を追加します。

    ```azurecli
    az extension add --name import-export
    ```

1. ディスクを受け取ることができる場所の一覧を取得するには、[az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) コマンドを使用します。

    ```azurecli
    az import-export location list
    ```

1. 次の [az import-export create](/cli/azure/import-export#az_import_export_create) コマンドを実行して、既存のストレージ アカウントを使用するエクスポート ジョブを作成します。

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

   このジョブは、ストレージ アカウント内のすべての BLOB をエクスポートします。 エクスポートする BLOB を指定するには、この **--export** の値を次のように置き換えます。

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   このパラメーター値では、ルート コンテナー内の *logo.bmp* という名前の BLOB がエクスポートされます。

   また、プレフィックスを使用して、コンテナー内のすべての BLOB を選択するオプションもあります。 この **--export** の値を次のように置き換えます。

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   詳細については、「[有効な BLOB パスの例](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths)」を参照してください。

   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスは BLOB のスナップショットを作成し、スナップショットをコピーします。

1. [az import-export list](/cli/azure/import-export#az_import_export_list) コマンドを使用して、リソース グループ myierg のすべてのジョブを表示します。

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. ジョブを更新するかジョブをキャンセルするには、[az import-export update](/cli/azure/import-export#az_import_export_update) コマンドを実行します。

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

以下の手順を使用して、Azure PowerShell でエクスポート ジョブを作成します。

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az.ImportExport** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>ジョブの作成

1. ディスクを受け取ることができる場所の一覧を取得するには、[Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) コマンドレットを使用します。

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. 次の [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) の例を実行して、既存のストレージ アカウントを使用するエクスポート ジョブを作成します。

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

   このジョブは、ストレージ アカウント内のすべての BLOB をエクスポートします。 エクスポートする BLOB を指定するには、この **-ExportBlobListblobPath** の値を次のように置き換えます。

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   このパラメーター値では、ルート コンテナー内の *logo.bmp* という名前の BLOB がエクスポートされます。

   また、プレフィックスを使用して、コンテナー内のすべての BLOB を選択するオプションもあります。 この **-ExportBlobListblobPath** の値を次のように置き換えます。

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   詳細については、「[有効な BLOB パスの例](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths)」を参照してください。

   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスは BLOB のスナップショットを作成し、スナップショットをコピーします。

1. [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) コマンドレットを使用して、リソース グループ myierg のすべてのジョブを表示します。

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. ジョブを更新するかジョブをキャンセルするには、[Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) コマンドレットを実行します。

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-2-ship-the-drives"></a>手順 2:ドライブを送付する

必要なドライブの数がわからない場合は、「[必要なドライブ数を特定する](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need)」を参照してください。 ドライブの数がわかっている場合は、ドライブの送付に進みます。

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>手順 3:追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>手順 4:ディスクを受け取る

ダッシュボードでジョブの完了が報告されると、ディスクが返送され、ポータルで配送の追跡番号を利用できるようになります。

1. エクスポートされたデータが保存されたドライブを受け取ったら、BitLocker キーを取得してドライブのロックを解除する必要があります。 Azure portal でエクスポート ジョブに移動します。 **[インポート/エクスポート]** タブをクリックします。
2. リストからエクスポート ジョブを選択してクリックします。 **[暗号化]** に移動し、キーをコピーします。

   ![エクスポート ジョブの BitLocker キーの表示](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. BitLocker キーを使用してディスクのロックを解除します。

これでエクスポートは完了です。

## <a name="step-5-unlock-the-disks"></a>手順 5:ディスクのロックを解除する

ドライブのロックを解除するには、次のコマンドを使用します。

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

サンプル入力の例を次に示します。

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

この時点で、ジョブを削除するか、そのままにしておくことができます。 ジョブは、90 日後に自動的に削除されます。

## <a name="next-steps"></a>次のステップ

* [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
* [Import/Export のコピー ログの確認](storage-import-export-tool-reviewing-job-status-v1.md)

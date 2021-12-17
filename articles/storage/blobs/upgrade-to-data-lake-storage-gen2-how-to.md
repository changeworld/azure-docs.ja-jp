---
title: Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする | Microsoft Docs
description: Resource Manager テンプレートを使用して Azure Blob Storage から Data Lake Storage にアップグレードする方法を説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: ecf0988069bf023225354b4ad864537737999094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008710"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする

この記事は、ファイルやディレクトリ レベルのセキュリティや、より高速な操作などの機能を利用できるようにするのに役立ちます。 これらの機能は、ビッグ データ分析ワークロードで広く使用されており、まとめて Azure Data Lake Storage Gen2 と呼ばれます。 

これらの機能の詳細を確認し、ワークロード、アプリケーション、コスト、サービス統合、ツール、機能、ドキュメントに対するこのアップグレードの影響を評価するには、「[Azure Data Lake Storage Gen2 の機能での Azure Blob Storage のアップグレード](upgrade-to-data-lake-storage-gen2.md)」を参照してください。

> [!IMPORTANT]
> アップグレードは一方向です。 アップグレードを実行した後で、アカウントを元に戻す方法はありません。 非運用環境でアップグレードを検証することをお勧めします。

## <a name="review-feature-support"></a>レビュー機能のサポート

お使いのアカウントが、Data Lake Storage Gen2 対応アカウントではまだサポートされていない機能を使用するように構成されていることがあります。 アカウントでまだサポートされていない機能を使用している場合、アップグレードの検証手順は成功しません。 

サポートされていない機能を識別するには、「[Azure Storage アカウントにおける Blob Storage 機能のサポート](storage-feature-support-in-storage-accounts.md)」を参照してください。 アカウントでサポートされていない機能を使用している場合は、アップグレードを開始する前にそれらを無効にしてください。

## <a name="perform-the-upgrade"></a>アップグレードを実行する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. ストレージ アカウントを見つけて、アカウントの概要を表示します。

3. **[Data Lake Gen2 の移行]** を選択します。

   **[Azure Data Lake Gen2 の機能を持つストレージ アカウントにアップグレードする]** 構成ページが表示されます。

   > [!div class="mx-imgBorder"]
   > ![[構成] ページ](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. **[手順 1: アップグレードする前にアカウントの変更を確認する]** セクションを展開し、 **[変更を確認して同意する]** をクリックします。

5. **[Review account changes]\(アカウントの変更の確認\)** ページで、チェック ボックスをオンにして、 **[Agree to changes]\(変更に同意する\)** をクリックします。

6. **[手順 2: アップグレードする前にアカウントを検証する]** セクションを展開し、 **[検証の開始]** をクリックします。

   検証に失敗した場合は、ページにエラーが表示されます。 場合によっては、 **[エラーの表示]** リンクが表示されます。 リンクが表示されたら、それを選択します。 

   > [!div class="mx-imgBorder"]
   > ![[エラーの表示] リンク](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   次に、**error.json** ファイルのコンテキスト メニューから、 **[ダウンロード]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![エラー json ページ](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   ダウンロードしたファイルを開き、アカウントが検証ステップに合格しなかった理由を確認します。 

   次の JSON は、アカウントで互換性のない機能が有効になっていることを示しています。 この場合は、その機能を無効にしてから、検証プロセスを再度開始します。

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. アカウントが正常に検証された後、 **[手順 3: アカウントのアップグレード]** セクションを展開して、 **[アップグレードの開始]** をクリックします。

   > [!IMPORTANT]
   > アカウントがアップグレードされている間、書き込み操作は無効になります。 読み取り操作は無効になりませんが、アップグレード プロセスが不安定になる可能性があるため、読み取り操作を中断することを強く推奨します。

   移行が正常に完了すると、次のようなメッセージが表示されます。 

   > [!div class="mx-imgBorder"]
   > ![移行完了ページ](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell コマンド ウィンドウを開きます。

2. 最新の Azure PowerShell モジュールがあることを確認します。 「[Azure PowerShell モジュールをインストールする](/powershell/azure/install-Az-ps)」を参照してください。

3. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

4. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

6. 次のコマンドを使用して、ストレージ アカウントを検証します。

   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Validation -AsJob
   ```

   - `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   - `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

   アカウントのサイズによっては、このプロセスに時間がかかる場合があります。 クライアントがブロックされないように、`asJob` スイッチを使用してコマンドをバックグラウンド ジョブで実行できます。 コマンドはリモートで実行されますが、ジョブはコマンドを実行するローカル コンピューターまたは VM 上に存在します。 結果は、ローカル コンピューターまたは VM に送信されます。

7. ジョブの状態を確認し、ジョブのすべてのプロパティを一覧で表示するには、戻り値の変数を `Format-List` コマンドレットにパイプします。 

   ```powershell
   $result | Format-List -Property *
   ```

   検証が成功した場合、**State** プロパティは **Completed** に設定されます。

   検証に失敗した場合、**State** プロパティは **Failed** に設定され、**Error** プロパティで検証エラーが表示されます。 

   次の出力は、アカウントで互換性のない機能が有効になっていることを示しています。 この場合は、その機能を無効にしてから、検証プロセスを再度開始します。

   > [!div class="mx-imgBorder"]
   > ![検証エラー](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-error-powershell.png)

   場合によっては、**Error** プロパティによって **error.json** という名前のファイルへのパスが提供されます。 そのファイルを開いて、アカウントが検証手順に合格しなかった理由を判断できます。 

   次の JSON は、アカウントで互換性のない機能が有効になっていることを示しています。 この場合は、その機能を無効にしてから、検証プロセスを再度開始します。

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

8. アカウントが正常に検証された後、次のコマンドを実行してアップグレードを開始します。
   
   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Upgrade -AsJob -Force
   ```

   上記の検証例と同様に、この例では `asJob` スイッチを使用して、コマンドをバックグラウンド ジョブで実行します。 `Force` スイッチによって、アップグレードを確認するプロンプトがオーバーライドされます。  `AsJob` スイッチを使用しない場合は、単にプロンプトに応答できるので、`Force` スイッチを使用する必要はありません。

   > [!IMPORTANT]
   > アカウントがアップグレードされている間、書き込み操作は無効になります。 読み取り操作は無効になりませんが、アップグレード プロセスが不安定になる可能性があるため、読み取り操作を中断することを強く推奨します。

   ジョブの状態を確認するには、前の手順で説明したのと同じテクニックを使用します。 プロセスが実行されると、**State** プロパティが **Running** に設定されます。

   移行が正常に完了すると、**State** プロパティが **Completed** に設定され、**Error** プロパティにエラーは表示されません。
   
   > [!div class="mx-imgBorder"]
   > ![正常な完了の出力](./media/upgrade-to-data-lake-storage-gen2-how-to/success-message-powershell.png)


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. まず、[Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 次のコマンドを使用して、インストールされている Azure CLI のバージョンが `2.29.0` 以上であることを確認します。

   ```azurecli
    az --version
   ```

   Azure CLI のバージョンが `2.29.0` より前の場合は、新しいバージョンをインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. 次のコマンドを使用して、ストレージ アカウントを検証します。

   ```azurecli
   az storage account hns-migration start --type validation -n <storage-account-name> -g <resource-group-name>
   ```

   - `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   - `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

   検証が成功した場合、プロセスは完了し、エラーは表示されません。
   
   検証に失敗した場合は、検証エラーがコンソールに表示されます。 たとえば、エラー `(IncompatibleValuesForAccountProperties) Values for account properties are incompatible: Versioning Enabled` は、アカウントで互換性のない機能 (バージョン管理) が有効になっていることを示しています。 この場合は、その機能を無効にしてから、検証プロセスを再度開始します。

   場合によっては、**error.json** という名前のファイルへのパスがコンソールに表示されます。 そのファイルを開いて、アカウントが検証手順に合格しなかった理由を判断できます。 

   次の JSON は、アカウントで互換性のない機能が有効になっていることを示しています。 この場合は、その機能を無効にしてから、検証プロセスを再度開始します。

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

5. アカウントが正常に検証された後、次のコマンドを実行してアップグレードを開始します。
   
   ```azurecli
   az storage account hns-migration start --type upgrade -n storage-account-name -g <resource-group-name>
   ```

   > [!IMPORTANT]
   > アカウントがアップグレードされている間、書き込み操作は無効になります。 読み取り操作は無効になりませんが、アップグレード プロセスが不安定になる可能性があるため、読み取り操作を中断することを強く推奨します。

   移行が成功した場合、プロセスは完了し、エラーは表示されません。

---

## <a name="stop-the-upgrade"></a>アップグレードを停止する

移行が完了する前に、それを停止できます。 停止すると何が起こるかについて、説明します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

アップグレードが完了する前にそれを停止するには、アップグレードの進行中に **[アップグレードの取り消し]** を選択します。

> [!div class="mx-imgBorder"]
> ![アップグレードの取り消し](./media/upgrade-to-data-lake-storage-gen2-how-to/cancel-the-upgrade.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

アップグレードが完了する前にそれを停止するには、`Stop-AzStorageAccountHierarchicalNamespaceUpgrade` コマンドを使用します。

```powershell
Stop-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName <resource-group-name> -Name <storage-account-name>
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

アップグレードが完了する前にそれを停止するには、`az storage account hns-migration stop` コマンドを使用します。

```azurecli
az storage account hns-migration stop -n <storage-account-name> -g <resource-group-name>
```

---


## <a name="migrate-data-workloads-and-applications"></a>データ、ワークロード、アプリケーションを移行する 

1. **Blob service** エンドポイントまたは **Data Lake Storage** エンドポイントを指し示すように、[ワークロード内のサービス](./data-lake-storage-supported-azure-services.md)を構成します。

   > [!div class="mx-imgBorder"]
   > ![アカウントのエンドポイント](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Windows Azure Storage Blob ドライバーまたは [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) ドライバーを使用する Hadoop ワークロードの場合は、[Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) ドライバーを使用するようにそれらを変更します。 **Blob service** エンドポイントに対して要求を行う WASB ドライバーとは異なり、ABFS ドライバーはアカウントの **Data Lake Storage** エンドポイントに対して要求を行います。

2. カスタム アプリケーションをテストして、アップグレードされたアカウントで想定どおりに動作することを確認します。 

   [Data Lake Storage でのマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)を使用すると、ほとんどのアプリケーションで BLOB API を変更せずに引き続き使用できます。 問題が発生する場合、または API を使用してディレクトリ操作と ACL を使用したい場合は、Data Lake Storage Gen2 API を使用するようにコードの一部を移動することを検討します。 [.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md)、[Python](data-lake-storage-directory-file-acl-python.md)、[Node.js](data-lake-storage-acl-javascript.md)、[REST](/rest/api/storageservices/data-lake-storage-gen2) に関するガイドを参照してください。 

3. カスタム スクリプトをテストして、アップグレードされたアカウントで想定どおりに動作することを確認します。 

   BLOB API の場合と同様に、スクリプトの多くは、変更を必要とせずに機能する可能性があります。 ただし、必要な場合は、Data Lake Storage Gen2 の [PowerShell コマンドレット](data-lake-storage-directory-file-acl-powershell.md)と [Azure CLI コマンド](data-lake-storage-directory-file-acl-cli.md)を使用するようにスクリプト ファイルをアップグレードできます。
 

## <a name="see-also"></a>関連項目

[Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)
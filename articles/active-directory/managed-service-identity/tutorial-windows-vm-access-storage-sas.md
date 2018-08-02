---
title: Windows VM マネージド ID を使用して Azure Storage にアクセスする際に SAS 資格情報を使用する
description: Windows VM マネージド サービス ID を使用して Azure Storage にアクセスする際に、ストレージ アカウント アクセス キーではなく、SAS 資格情報を使用する方法を示すチュートリアル。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 983cecdcdb95dca398f728dbdbe5feac69075d6a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248372"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>チュートリアル: Windows VM マネージド サービス ID を使用して SAS 資格情報で Azure Storage にアクセスする

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシンのマネージド サービス ID を有効にした後、そのマネージド サービス ID を使用してストレージの Shared Access Signature (SAS) 資格情報を取得する方法について説明します。 具体的には [Service SAS 資格情報](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)です。 

Service SAS は、アカウント アクセス キーを公開することなく、ストレージ アカウント内のオブジェクトへの限られたアクセス許可を限られた時間にわたって特定のサービス (ここでは BLOB サービス) に対してのみ提供できます。 ストレージ SDK の使用時など、ストレージ操作を実行するときに、SAS 資格情報を通常どおりに使用できます。 このチュートリアルでは、Azure Storage PowerShell を使用して BLOB のアップロードとダウンロードを行う手順を示します。 学習内容:


> [!div class="checklist"]
> * Windows 仮想マシンで管理対象サービス ID を有効にする 
> * Resource Manager で VM にストレージ アカウント SAS へのアクセス権を付与する 
> * VM の ID を使用してアクセス トークンを取得し、それを使用して Resource Manager から SAS を取得する 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このチュートリアルでは、新しい Windows VM を作成します。 既存の VM 上でマネージド サービス ID を有効にすることもできます。

1.  Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2.  **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3.  仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4.  ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5.  仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6.  VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

    ![イメージ テキスト](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>VM 上でマネージド サービス ID を有効にする

仮想マシンのマネージド サービス ID を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 マネージド サービス ID を有効にすると、内部では VM が Azure Active Directory に登録されて、そのマネージド ID が作成され、VM で ID が構成されます。

1. 新しい仮想マシンのリソース グループに移動し、前の手順で作成した仮想マシンを選択します。
2. 左側のパネルで VM の [設定] の下にある **[構成]** をクリックします。
3. マネージド サービス ID を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。
4. **[保存]** をクリックして構成を保存します。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

まだお持ちでない場合は、この時点でストレージ アカウントを作成します。 この手順をスキップし、既存のストレージ アカウントの SAS 資格情報に対するアクセス権を VM のマネージド サービス ID に付与することもできます。 

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[ストレージ]**、次に **[ストレージ アカウント]** をクリックすると、新しい [ストレージ アカウントの作成] パネルが表示されます。
3. ストレージ アカウントの名前を入力します。この場前は後ほど使用します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** がそれぞれ [Resource manager] と [汎用] に設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>ストレージ アカウントに BLOB コンテナーを作成する

後で、新しいストレージ アカウントにファイルをアップロードおよびダウンロードします。 ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。

1. 新たに作成したストレージ アカウントに戻ります。
2. 左側のパネルで、[Blob service] の下の **[コンテナー]** リンクをクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックすると、[新しいコンテナー] パネルがスライドして現れます。
4. コンテナーに名前を付け、アクセス レベルを選択して、**[OK]** をクリックします。 指定した名前は、後ほどチュートリアルで使用されます。 

    ![ストレージ コンテナーの作成](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-a-storage-sas"></a>VM のマネージド サービス ID にストレージ SAS を使用するためのアクセス権を付与する 

Azure Storage は、ネイティブでは Azure AD 認証をサポートしていません。  ただし、マネージド サービス ID を使用して Resource Manager からストレージ SAS を取得し、その SAS を使用してストレージにアクセスできます。  この手順では、ストレージ アカウントの SAS に対するアクセス権を VM のマネージド サービス ID に付与します。   

1. 新たに作成したストレージ アカウントに戻ります。   
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ 追加]** をクリックして、VM 用に新しいロールの割り当てを追加します。
4. ページの右側で、**[ロール]** を ［ストレージ アカウントの共同作業者］ に設定します。  
5. 次のドロップダウンで、**[アクセスの割り当て先]** を ［仮想マシン］ リソースに設定します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、**[リソース グループ]** を [すべてのリソース グループ] に設定します。  
7. 最後に、**[選択]** のドロップダウンで Windows 仮想マシンを選択し、**[保存]** をクリックします。 

    ![イメージ テキスト](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

ここでは、Azure Resource Manager PowerShell コマンドレットを使用する必要があります。  インストールしていない場合は、先に進む前に、[最新バージョンをダウンロード](https://docs.microsoft.com/powershell/azure/overview)してください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。
2. Windows VM を作成したときに追加した**ユーザー名**と**パスワード**を入力します。 
3. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで PowerShell を開きます。 
4. PowerShell の Invoke-WebRequest を使用して、ローカルのマネージド サービス ID エンドポイントに対して Azure Resource Manager のアクセス トークンを取得するよう要求します。

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource"パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    
    次に、$response オブジェクト内で JavaScript Object Notation (JSON) 形式の文字列として格納されている "Content" 要素を抽出します。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    次に、アクセス トークンを応答から抽出します。
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>ストレージ呼び出しを行うために Azure Resource Manager から SAS 資格情報を取得する 

ここで、PowerShell を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、ストレージ SAS 資格情報を作成します。 SAS 資格情報を作成したら、ストレージ操作を呼び出すことができます。

この要求のために、次の HTTP 要求のパラメーターを使用して SAS 資格情報を作成します。

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

これらのパラメーターは、SAS 資格情報に対する要求の POST 本文に含まれます。 SAS 資格情報を作成するためのパラメーターの詳細については、[List Service SAS REST リファレンス](/rest/api/storagerp/storageaccounts/listservicesas)に関する記事を参照してください。

最初にパラメーターを JSON に変換し、その後で SAS 資格情報を作成するストレージの `listServiceSas` エンドポイントを呼び出します。

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL では大文字小文字が区別されるため、リソース グループの命名時に以前使用したものと同じ大文字小文字が使用されていること ("resourceGroups" の "G" が大文字であることを含む) を確認してください。 

これで、応答から SAS 資格情報を抽出できます。

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

SAS 資格情報を調べると、次のように表示されます。

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

次に、"test.txt" というファイルを作成します。 その後、SAS 資格情報を使用して `New-AzureStorageContent` コマンドレットで認証を行い、ファイルを BLOB コンテナーにアップロードしてから、ファイルをダウンロードします。

```bash
echo "This is a test text file." > test.txt
```

必ず、最初に `Install-Module Azure.Storage` を使用して Azure Storage コマンドレットをインストールしてください。 その後、作成した BLOB を、次のように `Set-AzureStorageBlobContent` PowerShell コマンドレットを使用してアップロードします。

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

応答:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

アップロードした BLOB を、次のように `Get-AzureStorageBlobContent` PowerShell コマンドレットを使用してダウンロードすることもできます。

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

応答:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、SAS 資格情報を使用して Azure Storage にアクセスするためのマネージド サービス ID の作成方法について説明します。  Azure Storage SAS の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Shared Access Signatures (SAS) の使用](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)



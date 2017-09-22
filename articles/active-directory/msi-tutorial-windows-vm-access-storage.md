---
title: "Windows VM の MSI を使用した Azure ストレージへのアクセス"
description: "Windows VM 管理対象サービス ID (MSI) を使用して Azure ストレージにアクセスするプロセスについて説明するチュートリアルです。"
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Windows VM 管理対象サービスID (MSI) を使用して Azure ストレージにアクセスする

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシンの管理対象サービス ID (MSI) を有効にし、その ID を使用してストレージ キーにアクセスする方法について説明します。 ストレージ SDK の使用時など、ストレージ操作を実行するときに、ストレージ キーを通常どおりに使用できます。 このチュートリアルでは、Azure CLI を使用して BLOB をアップロードおよびダウンロードします。 学習内容:


> [!div class="checklist"]
> * Windows 仮想マシンで MSI を有効にする 
> * 新しいストレージ アカウントを作成する
> * VM にストレージへのアクセス権を付与する 
> * VM ID を使用してストレージ アカウントのアクセス トークンを取得する 


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このチュートリアルでは、新しい Windows VM を作成します。 既存の VM で MSI を有効にすることもできます。

1.  Azure Portal の左上にある **[新規]** ボタンをクリックします。
2.  **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3.  仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4.  ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5.  仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6.  VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

    ![イメージ テキスト](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>VM で MSI を有効にする

仮想マシンの MSI を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 内部的には、MSI を有効にすると、仮想マシンに MSI VM 拡張機能がインストールされ、VM の MSI が有効化されます。  

1. MSI を有効にする**仮想マシン**を選択します。
2. 左側のナビゲーション バーで、**[構成]** をクリックします。
3. **管理対象サービス ID** が表示されます。 MSI を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。
4. **[保存]** をクリックして構成を保存します。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. この **Windows VM** で有効になっている拡張機能を確認する場合は、**[拡張機能]** をクリックします。 MSI が有効になっている場合、**ManagedIdentityExtensionforWindows** が一覧に表示されます。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>新しいストレージ アカウントを作成する 

ストレージ キーは、ストレージ操作を実行するときに通常どおりに使用できます。この例では、Azure CLI を使用して BLOB をアップロードおよびダウンロードします。 

1. サイドバーに移動し、**[ストレージ]** を選択します。  
2. 新しい**ストレージ アカウント**を作成します。  
3. **[デプロイ モデル]** で **[Resource Manager]** を選択し、**[アカウントの種類]** で **[汎用]** を選択します。  
4. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で **Linux 仮想マシン**を作成したときに使用したものであることを確認します。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>ストレージ キーを使用するために VM ID にアクセス権を付与する 

MSI を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。   

1. **ストレージ**のタブに移動します。  
2. 以前に作成した**ストレージ アカウント**を選択します。   
3. 左側のパネルの **[アクセス制御 (IAM)]** に移動します。  
4. VM の新しいロールの割り当てを**追加**し、**[ロール]** で **[ストレージ アカウント キー オペレーターのサービス ロール]** を選択します。  
5. 次のドロップダウンで、**[アクセスの割り当て先]** として **[仮想マシン]** リソースを選択します。  
6. 次に、**[サブスクリプション]** ドロップダウンに適切なサブスクリプションが表示されていることを確認します。 **[リソース グループ]** で **[すべてのリソース グループ]** を選択します。  
7. 最後に、**[選択]** の一覧で使用する Windows 仮想マシンを選択し、**[保存]** をクリックします。 

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 

ここでは、**PowerShell** を使用する必要があります。  インストールしていない場合は、[ここから](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)ダウンロードします。 

1. ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** の **[接続]** をクリックします。 
2. Windows VM を作成したときに追加した**ユーザー名**と**パスワード**を入力します。 
3. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで **PowerShell** を開きます。 
4. Powershell の Invoke-WebRequest を使用して、ローカルの MSI エンドポイントに対して Azure Resource Manager のアクセス トークンを取得するよう要求します。

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource"パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    
    次に、$response オブジェクト内で JavaScript オブジェクト表記 (JSON) 形式の文字列として格納されている完全な応答を抽出します。 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    次に、アクセス トークンを応答から抽出します。
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    最後に、アクセス トークンを使用して Azure Resource Manager を呼び出します。 さらに、この例では、PowerShell の Invoke-WebRequest を使用して、Azure Resource Manager への呼び出しを行い、Authorization ヘッダーにアクセス トークンを含めます。
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > URL は大文字小文字を区別するため、以前にリソース グループに名前を付けたときに使用したのと同じ大文字小文字が使用されていること、および "resourceGroup" で大文字の "G" が使用されていることを確認してください。
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Azure Resource Manager からストレージ キーを取得する 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Azure CLI を使用してアップロードするファイルを作成する**

```bash
echo "This is a test text file." > test.txt
```

**Azure CLI を使用してファイルをアップロードし、ストレージ キーを使用して認証する**

> [!NOTE]
> 最初に、Azure ストレージのコマンドレット "Install-Module Azure.Storage" をインストールしてください。 

PowerShell は次のものを要求します。


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
```

応答:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

**Azure CLI を使用してファイルをダウンロードし、ストレージ キーを使用して認証する**

PowerShell は次のものを要求します。

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
```

応答:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```






---
title: Windows VM のマネージド サービス ID を使用して Azure Storage にアクセスする
description: Windows VM のマネージド サービス ID を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: ca2a460658b0de4f91816342d2eabb78ceee89fb
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247375"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>チュートリアル: Windows VM マネージド サービス ID を使用してアクセス キーで Azure Storage にアクセスする

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシンのマネージド サービス ID を有効にし、その ID を使用してストレージ アカウント アクセス キーを取得する方法を示します。 ストレージ SDK の使用時など、ストレージ操作を実行するときに、ストレージ アクセス キーを通常どおりに使用できます。 このチュートリアルでは、Azure Storage PowerShell を使用して BLOB をアップロードおよびダウンロードします。 学習内容:


> [!div class="checklist"]
> * Windows 仮想マシンで管理対象サービス ID を有効にする 
> * Resource Manager で VM にストレージ アカウント アクセス キーへのアクセス権を付与する 
> * VM の ID を使用してアクセス トークンを取得し、それを使用して Resource Manager からストレージ アクセス キーを取得する 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このチュートリアルでは、新しい Windows VM を作成します。 既存の VM でマネージド サービス ID を有効にすることもできます。

1.  Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2.  **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3.  仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4.  ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5.  仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6.  VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

    ![イメージ テキスト](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>VM でマネージド サービス ID を有効にする

仮想マシンのマネージド サービス ID を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 マネージド サービス ID を有効にすると、内部では VM が Azure Active Directory に登録されて、そのマネージド ID が作成され、VM で ID が構成されます。

1. 新しい仮想マシンのリソース グループに移動し、前の手順で作成した仮想マシンを選択します。
2. 左側の VM の [設定] の下にある **[構成]** をクリックします。
3. マネージド サービス ID を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。
4. **[保存]** をクリックして構成を保存します。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

まだお持ちでない場合は、この時点でストレージ アカウントを作成します。 この手順をスキップし、既存のストレージ アカウントのキーに対するアクセス権を VM のマネージド サービス ID に付与することもできます。 

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
2. 左側の **[コンテナー]** リンク ([Blob service] の下にある) をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックすると、[新しいコンテナー] パネルがスライドして現れます。
4. コンテナーに名前を付け、アクセス レベルを選択して、**[OK]** をクリックします。 指定した名前は、後ほどチュートリアルで使用されます。 

    ![ストレージ コンテナーの作成](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-storage-account-access-keys"></a>ストレージ アカウント キーを使用するために VM のマネージド サービス ID にアクセス権を付与する 

Azure Storage は、ネイティブでは Azure AD 認証をサポートしていません。  ただし、マネージド サービス ID を使用して Resource Manager からストレージ アカウント アクセス キーを取得し、そのキーを使用してストレージにアクセスできます。  この手順では、ストレージ アカウントのキーに対するアクセス権を自分の VM マネージド サービス ID に付与します。   

1. 新たに作成したストレージ アカウントに戻ります。  
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ 追加]** をクリックして、VM 用に新しいロールの割り当てを追加します。
4. ページの右側で、**[ロール]** を "ストレージ アカウント キー オペレーターのサービス ロール" に設定します。 
5. 次のドロップダウンで、**[アクセスの割り当て先]** を "仮想マシン" リソースに設定します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、**[リソース グループ]** を [すべてのリソース グループ] に設定します。  
7. 最後に、**[選択]** のドロップダウンで Windows 仮想マシンを選択し、**[保存]** をクリックします。 

    ![イメージ テキスト](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>ストレージ呼び出しを行うために Azure Resource Manager からストレージ アカウント アクセス キーを取得する  

ここで、PowerShell を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、ストレージ アクセス キーを取得します。 ストレージ アクセス キーを取得したら、ストレージのアップロード/ダウンロード操作を呼び出すことができます。

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL では大文字小文字が区別されるため、リソース グループの命名時に以前使用したものと同じ大文字小文字が使用されていること ("resourceGroups" の "G" が大文字であることを含む) を確認してください。 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

次に、"test.txt" というファイルを作成します。 ストレージ アクセス キーを使用して `New-AzureStorageContent` コマンドレットで認証を行い、ファイルを BLOB コンテナーにアップロードしてから、ファイルをダウンロードします。

```bash
echo "This is a test text file." > test.txt
```

必ず、最初に `Install-Module Azure.Storage` を使用して Azure Storage コマンドレットをインストールしてください。 その後、作成した BLOB を、次のように `Set-AzureStorageBlobContent` PowerShell コマンドレットを使用してアップロードします。

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
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
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アクセス キーを使用して Azure Storage にアクセスするためのマネージド サービス ID の作成方法について説明します。  Azure Storage アクセス キーの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[ストレージ アクセス キーの管理](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)


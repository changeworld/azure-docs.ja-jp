---
title: "Linux VM の MSI を使用した Azure Storage へのアクセス"
description: "Linux VM の管理対象サービス ID (MSI) を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアル。"
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
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>Linux VM の管理対象サービス ID (MSI) を使用したストレージ資格情報へのアクセス

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシンの管理対象サービス ID (MSI) を有効にし、その ID を使用してストレージ キーにアクセスする方法について説明します。 ストレージ SDK の使用時など、ストレージ操作を実行するときに、ストレージ キーを通常どおりに使用できます。 このチュートリアルでは、Azure CLI を使用して BLOB をアップロードおよびダウンロードします。 学習内容:


> [!div class="checklist"]
> * Linux 仮想マシンで MSI を有効にする 
> * 新しいストレージ アカウントを作成する
> * VM にストレージへのアクセス権を付与する
> * VM ID を使用してストレージ キーにアクセスするストレージ アカウントのアクセス トークンを取得する 


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

このチュートリアルでは、新しい Linux VM を作成します。 既存の VM で MSI を有効にすることもできます。

1. Azure Portal の左上にある **[新規]** ボタンをクリックします。
2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。
3. 仮想マシンの情報を入力します。 **[認証の種類]** で、**[SSH 公開キー]** または **[パスワード]** を選択します。 作成した資格情報を使用して VM にログインできます。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. ドロップダウンで仮想マシンの**サブスクリプション**を選択します。
5. 仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、[サポートされるディスクの種類] フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

## <a name="enable-msi-on-your-vm"></a>VM で MSI を有効にする

仮想マシンの MSI を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 内部的には、MSI を有効にすると、VM に MSI VM 拡張機能がインストールされ、VM の管理対象サービス ID が有効化されます。  

1. MSI を有効にする**仮想マシン**を選択します。
2. 左側のナビゲーション バーで、**[構成]** をクリックします。
3. **管理対象サービス ID** が表示されます。 MSI を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。
4. **[保存]** をクリックして構成を保存します。

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. この **Linux VM** で有効になっている拡張機能を確認する場合は、**[拡張機能]** をクリックします。 MSI が有効になっている場合、**ManagedIdentityExtensionforLinux** が一覧に表示されます。

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
7. 最後に、**[選択]** の一覧で、使用する Linux 仮想マシンを選択し、**[保存]** をクリックします。 
    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す

バッシュ ターミナルを使用し、[こちら](https://msdn.microsoft.com/commandline/wsl/install_guide)で Linux ディストリビューションを選択してダウンロードする必要があります。


1. ポータルで Linux 仮想マシンに移動し、**[概要]** の **[接続]** をクリックします。 バッシュを使用するよう求められます。アラートに示された SSH と VM IP を書き留めます。 
2. バッシュを開いて接続します。  
3. ターミナルで、**SSH** と接続先の **VM** を入力します (例: ssh admin@12.61.219.35)。  
4. 次に、**Linux VM** の作成時に追加した**パスワード**の入力を求められます。 パスワードを入力すると、正常にサインインできます。  
5. その後、ログインしている Linux VM の承認トークンを取得するために、CURL を使用して要求を行うことができます。 **Azure Resource Manager** エンドポイントは、https://management.azure.com です。  

    アクセス トークンの CURL 要求を次に示します。
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > アクセスを要求しようとしているリソースの URL が、末尾にスラッシュが含まれた適切な形式であることを確認します (例: https: <RESOURCE>/)。
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>Azure Resource Manager からストレージ キーを取得するための CURL 要求  

> [!NOTE]
> URL のテキストでは大文字小文字が区別されるので、リソース グループに使用されている大文字小文字が正しく反映されていることを確認します。 また、これは GET 要求ではなく POST 要求であることを認識し、-d (NULL を指定可能) を指定して長さの制限を取得するための値を渡すことが重要です。  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

CURL 応答では、キーのリストが返されます。  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


アップロードするファイルを作成します。これが、作成したコンテナー内のストレージ アカウントのストレージ キーと共にアップロードできるサンプル BLOB ファイルになります。 

Linux VM でこれを行うには、次のコマンドを使用します。 

```bash
echo "This is a test file." > test.txt
```
 次に、Azure CLI を使用してファイルをアップロードし、ストレージ キーを使用して認証できます。
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

応答: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

さらに、Azure CLI を使用してファイルをダウンロードし、ストレージ キーを使用して認証することもできます。 

要求: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

応答: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>関連コンテンツ

MSI の概要については、[管理対象サービス ID の概要](../active-directory/msi-overview.md)に関する記事をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。



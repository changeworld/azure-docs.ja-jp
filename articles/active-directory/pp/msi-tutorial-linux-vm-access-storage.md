---
title: "Linux VM 上でユーザー割り当て MSI を使用して Azure Storage にアクセスする"
description: "Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアルです。"
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 91fe06825d1db586b715617241b0ca39115414c0
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Storage にアクセスする

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

このチュートリアルでは、ユーザー割り当て管理対象サービス ID (MSI) を作成して Linux 仮想マシンから使用し、その ID を使用して Azure Storage にアクセスする方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * ユーザー割り当て管理対象サービス ID (MSI) を作成する
> * ユーザー割り当て MSI を Linux 仮想マシンに割り当てる
> * Azure Storage インスタンスへの MSI のアクセスを許可する
> * ユーザー割り当て MSI の ID を使用してアクセス トークンを取得し、それ使用して Azure Storage にアクセスする

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある [試してみる] ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を実行する。
- ローカル CLI コンソールを使用する場合、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)する。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

最初に、新しい Linux VM を作成します。 必要に応じて、既存の VM で MSI を有効にすることもできます。

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。
3. 仮想マシンの情報を入力します。 **[認証の種類]** で、**[SSH 公開キー]** または **[パスワード]** を選択します。 作成した資格情報を使用して VM にログインできます。

    ![イメージ テキスト](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. ドロップダウンで仮想マシンの**サブスクリプション**を選択します。
5. 仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、[サポートされるディスクの種類] フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

## <a name="create-a-user-assigned-msi"></a>ユーザー割り当て MSI を作成する

1. Azure Cloud Shell セッションではなく CLI コンソールを使用している場合は、最初に Azure にサインインします。 新しい MSI を作成する Azure サブスクリプションに関連付けられているアカウントを使用します。

    ```azurecli
    az login
    ```

2. [az identity create](/cli/azure/identity#az_identity_create) を使用してユーザー割り当て MSI を作成します。 `-g` パラメーターは MSI が作成されるリソース グループを指定し、`-n` パラメーターはその名前を指定します。 `<RESOURCE GROUP>` と `<MSI NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    応答には、次の例のように、作成されたユーザー割り当て MSI の詳細が含まれています。 次の手順で `id` の値を使用するため、この値をメモに記録します。

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>ユーザー割り当て MSI を Linux VM に割り当てる

システム割り当て MSI とは異なり、ユーザー割り当て MSI は、複数の Azure リソース上のクライアントで使用できます。 このチュートリアルでは、ユーザー割り当て MSI を単一の VM に割り当てます。 ユーザー割り当て MSI は複数の VM に割り当てることもできます。

[az vm assign-identity](/cli/azure/vm#az_vm_assign_identity) を使用して、ユーザー割り当て MSI を Linux VM に割り当てます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `--identities` パラメーターの値には、前の手順で返された `id` プロパティを使用します。

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

まだお持ちでない場合は、この時点でストレージ アカウントを作成します。 この手順をスキップし、必要に応じて既存のストレージ アカウントを使用することもできます。 

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[ストレージ]**、**[ストレージ アカウント]** の順にクリックすると、[ストレージ アカウントの作成] パネルが新たに表示されます。
3. ストレージ アカウントの**名前**を入力します。この名前は後ほど使用します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** がそれぞれ [Resource manager] と [汎用] に設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>ストレージ アカウントに BLOB コンテナーを作成する

ファイルには Blob Storage が必要であるため、ファイルを格納する Blob コンテナーを作成する必要があります。 次に、新しいストレージ アカウントで、Blob コンテナーにファイルをアップロードおよびダウンロードします。

1. 新たに作成したストレージ アカウントに戻ります。
2. 左側の **[コンテナー]** リンク ([Blob service] の下にある) をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックすると、[新しいコンテナー] パネルがスライドして現れます。
4. コンテナーに名前を付け、アクセス レベルを選択して、**[OK]** をクリックします。 指定した名前は、後ほどチュートリアルでも使用されます。 

    ![ストレージ コンテナーの作成](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 新しく作成したコンテナーにファイルをアップロードします。コンテナー名をクリックしてから **[アップロード]** をクリックし、次にファイルを選択して **[アップロード]** をクリックします。

    ![テキスト ファイルをアップロードする](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Azure Storage コンテナーへのユーザー割り当て MSI のアクセスを許可する

MSI を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。 このチュートリアルでは、Azure Storage を使用します。

まず、MSI ID に Azure Storage コンテナーへのアクセス権を付与します。 この例では、先ほど作成したコンテナーを使用します。 使用する環境に合わせて、`<MSI PRINCIPALID>`、`<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<STORAGE ACCOUNT NAME>`、`<CONTAINER NAME>` の値を更新します。 `<CLIENT ID>` を、「[ユーザー割り当て MSI を作成する](#create-a-user-assigned-msi)」の `az identity create` コマンドによって返された `clientId` プロパティで置き換えます。

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

応答には、作成されたロール割り当ての詳細が含まれています。

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>ユーザー割り当て MSI の ID を使用してアクセス トークンを取得し、それを使用して Azure Storage を呼び出す

チュートリアルの残りの部分では、以前に作成した VM から作業を行う必要があります。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](~/articles/virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 VM に接続する文字列をコピーします。
2. 任意の SSH クライアントを使用して、VM に**接続**します。 
3. ターミナル ウィンドウで、CURL を使用して、ローカルの MSI エンドポイントに対して Azure Storage のアクセス トークンを取得するよう要求します。

   アクセス トークンを取得するための CURL 要求を次の例に示します。 `<CLIENT ID>` は、「[ユーザー割り当て MSI を作成する](#create-a-user-assigned-msi)」の `az identity create` コマンドによって返された `clientId` プロパティに必ず置き換えてください。

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > 前述の要求では、`resource` パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Storage のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
   > 次の応答では、簡潔にするため access_token 要素が短縮されています。

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. このアクセス トークンを使用して Azure Storage にアクセスします。たとえば、コンテナーに事前にアップロードされたサンプル ファイルの内容を読み取るなどです。 `<STORAGE ACCOUNT>`、`<CONTAINER NAME>`、`<FILE NAME>` の値を、以前に指定した値で置き換えます。`<ACCESS TOKEN>` は、前の手順で返されたトークンに置き換えます。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2017-11-09 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   応答には、次のようなファイルの内容が含まれています。

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>次の手順

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- これと同じチュートリアルをストレージ SAS 資格情報を使用して実行する方法については、「[Use a Linux VM Managed Service Identity to access Azure Storage via a SAS credential](msi-tutorial-linux-vm-access-storage-sas.md)」 (Linux VM 管理対象サービス ID を使用して SAS 資格情報で Azure Storage にアクセスする) を参照してください。
- Azure Storage アカウントの SAS 機能の詳細については、以下を参照してください。
  - [Shared Access Signatures (SAS) の使用](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Constructing a Service SAS (サービス SAS の構築)](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。






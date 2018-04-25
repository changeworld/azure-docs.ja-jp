---
title: Linux VM ユーザー割り当て MSI を使用した Azure Resource Manager へのアクセス
description: Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Resource Manager にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2b6d70e441dc3d300f49adff1c02d7cc65788d2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Linux VM 上でユーザー割り当て管理対象サービス ID (MSI) を使用して Azure Resource Manager にアクセスする

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

このチュートリアルでは、ユーザー割り当て管理対象サービス ID (MSI) を作成して Linux 仮想マシン (VM) に割り当て、その ID を使用して Azure Resource Manager API にアクセスする方法について説明します。 

管理対象サービス ID は Azure によって自動的に管理されます。 管理対象サービス ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * ユーザー割り当て MSI を作成する
> * MSI を Linux VM に割り当てる 
> * Azure Resource Manager で MSI にリソース グループへのアクセスを許可する 
> * MSI を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある [試してみる] ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

このチュートリアルでは、最初に新しい Linux VM を作成します。 既存の VM を使用することもできます。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。
3. 仮想マシンの情報を入力します。 **[認証の種類]** で、**[SSH 公開キー]** または **[パスワード]** を選択します。 作成した資格情報を使用して VM にログインできます。

    ![Linux VM を作成する](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
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

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Azure Resource Manager でユーザー割り当て MSI にリソース グループへのアクセスを許可する 

MSI は、Azure AD 認証をサポートするリソース API に対して認証するためのアクセス トークンをコードに提供します。 このチュートリアルでは、コードで Azure Resource Manager API にアクセスできるようにします。 

コードで API にアクセスできるようにするには、事前に MSI の ID に Azure Resource Manager のリソースへのアクセスを許可する必要があります。 このケースでは、VM が含まれているリソース グループが対象になります。 使用する環境に合わせて、`<SUBSCRIPTION ID>` と `<RESOURCE GROUP>` の値を更新します。 さらに、`<MSI PRINCIPALID>` を、「[ユーザー割り当て MSI を作成する](#create-a-user-assigned-msi)」の `az identity create` コマンドによって返された `principalId` プロパティで置き換えます。

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

応答には、次の例のように、作成されたロールの割り当ての詳細が含まれています。

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM の ID を使用してアクセス トークンを取得し、このトークンを使用して Resource Manager を呼び出す 

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](~/articles/virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。 VM に接続する文字列をコピーします。
2. 任意の SSH クライアントを使用して、VM に**接続**します。  
3. ターミナル ウィンドウで、CURL を使用して、ローカルの MSI エンドポイントに対して Azure Resource Manager のアクセス トークンを取得するよう要求します。  

   アクセス トークンを取得するための CURL 要求を次の例に示します。 `<CLIENT ID>` は、必ず「[ユーザー割り当て MSI を作成する](#create-a-user-assigned-msi)」の `az identity create` コマンドによって返された `clientId` プロパティで置き換えてください。 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。 
    
    応答には、Azure Resource Manager へのアクセスに必要なアクセス トークンが含まれています。 
    
    応答の例:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. 次に、このアクセス トークンを使用して Azure Resource Manager にアクセスし、以前にユーザー割り当て MSI にアクセスを許可したリソース グループのプロパティを読み取ります。 `<SUBSCRIPTION ID>` と `<RESOURCE GROUP>` は、以前に指定した値で必ず置き換えてください。`<ACCESS TOKEN>` は、前の手順で返されたトークンで置き換えてください。

    > [!NOTE]
    > URL では大文字小文字が区別されるため、リソース グループの命名時に使用したものと同じ大文字と小文字の使い分けが使用されていること、および `resourceGroups` の "G" が大文字であることを確認してください。  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    応答には、次の例のように、特定のリソース グループの情報が含まれています。 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>次の手順

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

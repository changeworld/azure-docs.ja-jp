---
title: "Linux VM の管理対象サービス ID を使用した Azure Data Lake Store へのアクセス"
description: "Linux VM の管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする方法を説明するチュートリアルです。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: fdf1c49c644a97ff2f66a8b217ee54896ed54131
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Linux VM の管理対象サービス ID を使用した Azure Data Lake Store へのアクセス

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) の管理対象サービス ID を使用して、Azure Data Lake Store にアクセスする方法について説明します。 MSI を通じて作成した ID は、Azure によって自動的に管理されます。 Azure Active Directory (Azure AD) 認証対応のサービスには、コードに資格情報を埋め込まなくても、MSI を使用して認証を行うことができます。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Linux VM で MSI を有効にする 
> * VM に Azure Data Lake Store へのアクセスを許可する
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure Data Lake Store にアクセスする

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Linux 仮想マシンを作成する

このチュートリアルでは、新しい Linux VM を作成します。 既存の VM で MSI を有効にすることもできます。

1. Azure Portal の左上隅にある **[新規]** ボタンを選択します。
2. **[コンピューティング]**、**[Ubuntu Server 16.04 LTS]** の順に選択します。
3. 仮想マシンの情報を入力します。 **[認証の種類]** で、**[SSH 公開キー]** または **[パスワード]** を選択します。 作成した資格情報を使用して VM にログインできます。

   ![仮想マシンを作成するための [基本] ウィンドウ](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. **[サブスクリプション]** ボックスの一覧で、仮想マシンのサブスクリプションを選択します。
5. 仮想マシンを作成する新しいリソース グループを選択するには、**[リソース グループ]** > **[新規作成]** を選択します。 終了したら、**[OK]** を選択します。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 [設定] ウィンドウで、既定値をそのままにして **[OK]** を選択します。

## <a name="enable-msi-on-your-vm"></a>VM で MSI を有効にする

仮想マシンの MSI を使用すると、コードに資格情報を埋め込まなくても、Azure AD からアクセス トークンを取得することができます。 MSI を有効にすると、VM に MSI VM 拡張機能がインストールされ、Azure Resource Manager で MSI が有効化されます。  

1. **Virtual Machine** の場合、MSI を有効にする仮想マシンを選択します。
2. 左側のウィンドウで、**[構成]** を選択します。
3. **管理対象のサービス ID** が表示されます。 MSI を登録して有効にするには、**[はい]** を選択します。 無効にする場合は、**[いいえ]** を選択します。
   ![[Azure Active Directory に登録する] の選択](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. **[保存]** を選択します。
5. この Linux VM で有効になっている拡張機能を確認する場合は、**[拡張機能]** を選択します。 MSI が有効になっている場合、**ManagedIdentityExtensionforLinux** が一覧に表示されます。

   ![拡張機能の一覧表示](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>VM に Azure Data Lake Store へのアクセスを許可する

この時点で、VM に Azure Data Lake Store のファイルとフォルダーへのアクセスを付与できます。 この手順では、既存の Data Lake Store インスタンスを使用することも、新しいものを作成することもできます。 Azure Portal を使用して Data Lake Store インスタンスを作成するには、[Azure Data Lake Store のクイック スタート](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)の手順を実行します。 [Azure Data Lake Store のドキュメント](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)に、Azure CLI と Azure PowerShell を使用するクイック スタートも用意されています。

Data Lake Store に新しいフォルダーを作成し、MSI にそのフォルダー内のファイルの読み取り、書き込み、および実行ができるアクセス許可を付与します。

1. Azure Portal の左側のウィンドウで **[Data Lake Store]** を選択します。
2. 使用する Data Lake Store インスタンスを選択します。
3. コマンド バーの **[データ エクスプローラー]** を選択します。
4. Data Lake Store インスタンスのルート フォルダーが選択されます。 コマンド バーの **[アクセス]**を選択します。
5. **[追加]**を選択します。  **[選択]** ボックスにお使いの VM の名前 (例: **DevTestVM**) を入力します。 検索結果からお使いの VM を選択し、**[選択]** をクリックします。
6. **[アクセス許可の選択]** をクリックします。  **[読み取り]** と **[実行]** を選択して **[このフォルダー]** に追加し、**[An access permission only]\(アクセス許可のみ\)** として追加します。 **[OK]** を選びます。  アクセス許可が正常に追加されます。
7. **[アクセス]** ウィンドウを閉じます。
8. このチュートリアル用に新しいフォルダーを作成します。 コマンド バーの **[新しいフォルダー]** を選択し、この新しいフォルダーに名前 (例: **TestFolder**) を付けます。  **[OK]** を選びます。
9. 作成したフォルダーを選択し、コマンド バーの **[アクセス]** を選択します。
10. 手順 5. と同様、**[追加]** を選択します。 **[選択]** ボックスにお使いの VM の名前を入力します。 検索結果からお使いの VM を選択し、**[選択]** をクリックします。
11. 手順 6. と同様に、**[アクセス許可の選択]** を選択します。 **[読み取り]**、**[書き込み]** および **[実行]** を選択して **[このフォルダー]** に追加したあと、**[アクセス許可エントリと既定のアクセス許可エントリ]** として追加します。 **[OK]** を選びます。  アクセス許可が正常に追加されます。

この時点で MSI は、作成したフォルダーのファイルに対してすべての操作を実行できます。 Data Lake Store のアクセス管理の詳細については、[Data Lake Store のアクセスの制御](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)に関するページをご覧ください。

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>アクセス トークンを取得して Data Lake Store ファイル システムを呼び出す

Azure Data Lake Store は Azure AD 認証をネイティブにサポートするため、MSI を使用して取得したアクセス トークンを直接受け入れることができます。 Data Lake Store のファイルシステムに対する認証を行うために、お使いの Data Lake Store ファイルシステムのエンドポイントに Azure AD によって発行されたアクセス トークンを送信します。 アクセス トークンは、Authorization ヘッダーに "Bearer \<ACCESS_TOKEN_VALUE\>" という形式で指定します。  Azure AD 認証の Data Lake Store のサポートに関する詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)」をご覧ください。

このチュートリアルでは、cURL から REST 要求を実行することにより、Data Lake Store ファイルシステムの REST API に対する認証を行います。

> [!NOTE]
> Data Lake Store ファイルシステムのクライアント SDK では、管理対象サービス ID はまだサポートされていません。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. ポータルで、お使いの Linux VM に移動します。 **[概要]** の **[接続]** を選択します。  
2. 任意の SSH クライアントを使用して、VM に接続します。 
3. ターミナル ウィンドウで、cURL を使用して、ローカルの MSI エンドポイントに対して Data Lake Store ファイルシステムのアクセス トークンを取得するよう要求します。 Data Lake Store のリソース識別子は "https://datalake.azure.net/" です。  リソース識別子の末尾にスラッシュが含まれることが重要です。
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   成功応答では、次のように Data Lake Store への認証に使用するアクセス トークンが返されます。

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. cURL を使用してルート フォルダー内のフォルダーを一覧表示するには、お使いの Data Lake Store ファイルシステムの REST エンドポイントに要求を行います。 これは、すべてが正しく構成されていることを簡単に確認する方法です。 前の手順で入手したアクセス トークンの値をコピーします。 Authorization ヘッダーの文字列 "Bearer" に、大文字の "B" があることが重要です。 Azure Portal の [Data Lake Store] ウィンドウの **[概要]** セクションで、ご利用の **Data Lake Store** インスタンスの名前を確認できます。

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   成功応答は次のようになります。

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. これで、Data Lake Store インスタンスへのファイルのアップロードを試すことができます。 まず、アップロードするファイルを作成します。

   ```bash
   echo "Test file." > Test1.txt
   ```

6. cURL を使用して先ほど作成したフォルダーにファイルをアップロードするには、Data Lake Store ファイルシステムの REST エンドポイントに要求を行います。 アップロードではリダイレクトが必要ですが、cURL が自動的にリダイレクトします。 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    成功応答は次のようになります。

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Data Lake Store ファイルシステムには、他にもさまざまな API があり、それらを使用して、ファイルへの追加、ファイルのダウンロードなどを実行できます。

お疲れさまでした。 Linux VM の MSI を使用して、Data Lake Store ファイルシステムに対する認証を行うことができました。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](../active-directory/msi-overview.md)に関する記事をご覧ください。
- Data Lake Store の管理操作には、Azure Resource Manager を使用します。  Resource Manager に対する認証を MSI で行う方法の詳細については、[Linux VM での管理対象サービス ID を使用した Resource Manager へのアクセス](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm)に関するページをご覧ください。
- 詳細については、[Data Lake Store での Azure Active Directory を使用した認証](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)に関するページをご覧ください。
- 詳細については、「[REST API を使用した Azure Data Lake Store に対するファイルシステム操作](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api)」または「[WebHDFS FileSystem APIs (WebHDFS ファイルシステム API)](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)」をご覧ください。
- 詳細については、「[Azure Data Lake Store のアクセス制御](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)」をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
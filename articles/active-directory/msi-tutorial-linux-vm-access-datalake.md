---
title: "Linux VM 管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする方法"
description: "Linux VM 管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする方法を説明するチュートリアルです。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: skwan
ms.openlocfilehash: 6d1bd5959c540033cc913062cf64cc01ab54c3f9
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Linux VM 管理対象サービス ID (MSI) を使用した Azure Data Lake Store へのアクセス

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) の管理対象サービス ID (MSI) を使用して、Azure Data Lake Store にアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Linux VM で MSI を有効にする 
> * VM に Azure Data Lake Store へのアクセスを許可する
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure Data Lake Store にアクセスする

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

仮想マシンの MSI を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 内部的には、MSI を有効にすると、VM に MSI VM 拡張機能がインストールされ、Azure Resource Manager で MSI が有効化されます。  

1. MSI を有効にする**仮想マシン**を選択します。
2. 左側のナビゲーション バーで、**[構成]** をクリックします。
3. **管理対象のサービス ID** が表示されます。 MSI を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。
4. **[保存]** をクリックして構成を保存します。

   ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. この **Linux VM** で有効になっている拡張機能を確認する場合は、**[拡張機能]** をクリックします。 MSI が有効になっている場合、**ManagedIdentityExtensionforLinux** が一覧に表示されます。

   ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>VM に Azure Data Lake Store へのアクセスを許可する

この時点で、VM に Azure Data Lake Store のファイルとフォルダーへのアクセスを付与できます。  この手順では、既存の Data Lake Store を使用することも、新しいものを作成することもできます。  Azure Portal を使用して新しい Data Lake Store を作成するには、この [Azure Data Lake Store のクイック スタート](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)の手順を実行します。 [Azure Data Lake Store のドキュメント](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)に、Azure CLI と Azure PowerShell を使用するクイック スタートも用意されています。

お使いの Data Lake Store に新しいフォルダーを作成し、VM の MSI にそのフォルダー内のファイルの読み取り、書き込み、および実行ができるアクセス許可を付与します。

1. Azure Portal で、左側のナビゲーションの **[Data Lake Store]** をクリックします。
2. このチュートリアルで使用する Data Lake Store をクリックします。
3. コマンド バーの **[データ エクスプローラー]** をクリックします。
4. Data Lake Store のルート フォルダーが選択されます。  コマンド バーの **[アクセス]** をクリックします。
5. **[追加]**をクリックします。  **[選択]** フィールドにお使いの VM の名前 (例: **DevTestVM**) を入力します。  検索結果からお使いの VM をクリックして選択し、**[選択]** をクリックします。
6. **[アクセス許可の選択]** をクリックします。  **[読み取り]** と **[実行]** を選択して **[このフォルダー]** に追加し、**[An access permission only]\(アクセス許可のみ\)** として追加します。  **[OK]**をクリックします。  アクセス許可が正常に追加されます。
7. **[アクセス]** ブレードを閉じます。
8. このチュートリアル用に新しいフォルダーを作成します。  コマンド バーの **[新しいフォルダー]** をクリックし、この新しいフォルダーに名前 (例: **TestFolder**) を付けます。  **[OK]**をクリックします。
9. 作成したフォルダーをクリックし、コマンド バーの **[アクセス]** をクリックします。
10. 手順 5 と同様に **[追加]** をクリックし、**[選択]** フィールドに VM の名前を入力したあと、この VM を選択して **[選択]** をクリックします。
11. 手順 6 と同様に **[アクセス許可の選択]** を選択し、**[読み取り]**、**[書き込み]** および**[実行]**を選択して **[このフォルダー]** に追加したあと、**[アクセス許可エントリと既定のアクセス許可エントリ]** として追加します。  **[OK]**をクリックします。  アクセス許可が正常に追加されます。

この時点でお使いの VM の MSI は、作成したフォルダーのファイルに対してすべての操作を実行できます。  Data Lake Store のアクセス管理の詳細については、[Data Lake Store のアクセスの制御](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)に関するページの記事をご覧ください。

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>VM の MSI を使用してアクセス トークンを取得し、それを使用して Azure Data Lake Store のファイルシステムを呼び出す

Azure Data Lake Store は Azure AD 認証をネイティブにサポートするため、MSI を使用して取得したアクセス トークンを直接受け入れることができます。  Data Lake Store のファイルシステムに認証するために、お使いの Data Lake Store ファイルシステムのエンドポイントに Azure AD によって発行されたアクセス トークンを送信します。このとき、Authorization ヘッダーに「Bearer \<ACCESS_TOKEN_VALUE\>」を指定します。  Azure AD 認証の Data Lake Store のサポートに関する詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)」をご覧ください。

このチュートリアルでは、REST 要求を実行するため、CURL を使用して、Data Lake Store ファイルシステムの REST API に対して認証します。

> [!NOTE]
> Data Lake Store ファイルシステムのクライアント SDK では、管理対象サービス ID はまだサポートされていません。  このチュートリアルは、SDK のサポートが追加されたときに更新されます。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. ポータルで Linux VM に移動し、**[概要]** の **[接続]** をクリックします。  
2. 任意の SSH クライアントを使用して、VM に**接続**します。 
3. ターミナル ウィンドウで、CURL を使用して、ローカルの MSI エンドポイントに対して Data Lake Store ファイルシステムのアクセス トークンを取得するよう要求します。  Data Lake Store のリソース識別子は "https://datalake.azure.net/" です。  リソース識別子の末尾にスラッシュが含まれることが重要です。
    
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

4. CURL を使用してルート フォルダー内のフォルダーを一覧表示するには、お使いの Data Lake Store ファイルシステムの REST エンドポイントに要求を行います。  これは、すべてが正しく構成されていることを簡単に確認する方法です。  前の手順で入手したアクセス トークンの値をコピーします。  Authorization ヘッダーの文字列 "Bearer" に、大文字の "B" があることが重要です。  Azure Portal の [Data Lake Store] ブレードの **[概要]** セクションで、ご利用の Data Lake Store の名前を確認できます。

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   正常な応答は次のようになります。

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. これで、Data Lake Store へのファイルのアップロードを試すことができます。  まず、アップロードするファイルを作成します。

   ```bash
   echo "Test file." > Test1.txt
   ```

6. CURL を使用して先ほど作成したフォルダーにファイルをアップロードするには、Data Lake Store ファイルシステムの REST エンドポイントに要求を行います。  アップロードではリダイレクトが必要ですが、CURL が自動的にリダイレクトします。 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    正常な応答は次のようになります。

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

その他の Data Lake Store ファイルシステムの API を使用すると、ファイルへの追加、ファイルのダウンロードなどができます。

ご利用ありがとうございます。  VM の MSI を使用して、Data Lake Store ファイルシステムに認証されました。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](../active-directory/msi-overview.md)に関する記事をご覧ください。
- Data Lake Store の管理操作には、Azure Resource Manager を使用します。  VM の MSI を使用して、Resource Manager に認証する方法の詳細については、[Linux VM での管理対象サービス ID を使用した Resource Manager へのアクセス](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm)に関するページをご覧ください。
- 詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)」をご覧ください。
- 詳細については、「[REST API を使用した Azure Data Lake Store に対するファイルシステム操作](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api)」または「[WebHDFS FileSystem APIs (WebHDFS ファイルシステム API)](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)」をご覧ください。
- 詳細については、「[Azure Data Lake Store のアクセス制御](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)」をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
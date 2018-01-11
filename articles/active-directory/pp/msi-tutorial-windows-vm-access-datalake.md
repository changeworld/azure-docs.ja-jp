---
title: "Windows VM 管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする方法"
description: "Windows VM 管理対象サービス ID (MSI) を使用して Azure Data Lake Store にアクセスする方法を説明するチュートリアルです。"
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
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 01cb63e3b56a0e629c0dee92723db19f5e3dc3c1
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Windows VM 管理対象サービス ID (MSI) を使用した Azure Data Lake Store へのアクセス

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

このチュートリアルでは、Windows 仮想マシン (VM) の管理対象サービス ID (MSI) を使用して、Azure Data Lake Store にアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理され、コードに資格情報を挿入しなくても、Azure AD の認証をサポートするサービスを認証できます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Windows VM で MSI を有効にする 
> * VM に Azure Data Lake Store へのアクセスを許可する
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure Data Lake Store にアクセスする

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このチュートリアルでは、新しい Windows VM を作成します。  既存の VM で MSI を有効にすることもできます。

1. Azure Portal の左上にある **[新規]** ボタンをクリックします。
2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3. 仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4. ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5. 仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ページで、既定値のまま **[OK]** をクリックします。

   ![イメージ テキスト](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>VM で MSI を有効にする 

VM MSI を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 MSI を有効にすると、VM の管理対象 ID を作成するよう Azure に指示が出されます。 内部的には、MSI を有効にすると、VM に MSI VM 拡張機能がインストールされ、Azure Resource Manager で MSI が有効化されます。

1. MSI を有効にする**仮想マシン**を選択します。  
2. 左側のナビゲーション バーで、**[構成]** をクリックします。 
3. **管理対象のサービス ID** が表示されます。 MSI を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。 
4. **[保存]** をクリックして構成を保存します。  
   ![イメージ テキスト](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. この VM で有効になっている拡張機能を確認して検証する場合は、**[拡張機能]** をクリックします。 MSI が有効になっている場合、**ManagedIdentityExtensionforWindows** が一覧に表示されます。

   ![イメージ テキスト](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>VM に Azure Data Lake Store へのアクセスを許可する

この時点で、VM に Azure Data Lake Store のファイルとフォルダーへのアクセスを付与できます。  この手順では、既存の Data Lake Store を使用することも、新しいものを作成することもできます。  Azure Portal を使用して新しい Data Lake Store を作成するには、この [Azure Data Lake Store のクイック スタート](~/articles/data-lake-store/data-lake-store-get-started-portal.md)の手順を実行します。 [Azure Data Lake Store のドキュメント](~/articles/data-lake-store/data-lake-store-overview.md)に、Azure CLI と Azure PowerShell を使用するクイック スタートも用意されています。

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

この時点でお使いの VM の MSI は、作成したフォルダーのファイルに対してすべての操作を実行できます。  Data Lake Store のアクセス管理の詳細については、[Data Lake Store のアクセスの制御](~/articles/data-lake-store/data-lake-store-access-control.md)に関するページの記事をご覧ください。

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>VM の MSI を使用してアクセス トークンを取得し、それを使用して Azure Data Lake Store のファイルシステムを呼び出す

Azure Data Lake Store は Azure AD 認証をネイティブにサポートするため、MSI を使用して取得したアクセス トークンを直接受け入れることができます。  Data Lake Store のファイルシステムと認証を行うには、Azure AD によって発行されたアクセス トークンの Authorization ヘッダーを、"Bearer < ACCESS_TOKEN_VALUE >" の形式で Data Lake Store ファイルシステムのエンドポイントに送信します。  Azure AD 認証の Data Lake Store のサポートに関する詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)」をご覧ください。

> [!NOTE]
> Data Lake Store ファイルシステムのクライアント SDK では、管理対象サービス ID はまだサポートされていません。  このチュートリアルは、SDK のサポートが追加されたときに更新されます。

このチュートリアルでは、PowerShell を使用して Data Lake Store ファイルシステムの REST API と認証し、REST 要求を行います。 VM の MSI を認証で使用するには、VM から要求を行う必要があります。

1. ポータルで **[仮想マシン]** を開いてお客様の Windows VM に移動し、**[概要]** の **[接続]** をクリックします。
2. Windows VM を作成したときに追加した**ユーザー名**と**パスワード**を入力します。 
3. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで **PowerShell** を開きます。 
4. Powershell の `Invoke-WebRequest` を使用して、ローカルの MSI エンドポイントに対して Azure Data Lake Store のアクセス トークンを取得するように要求します。  Data Lake Store のリソース識別子は "https://datalake.azure.net/" です。  Data Lake はリソース識別子と正確に一致します。末尾のスラッシュが重要です。

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   応答を JSON オブジェクトから PowerShell オブジェクトに変換します。 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   応答からアクセス トークンを抽出します。
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. PowerShell の 'Invoke-WebRequest' を使用して、ルート フォルダー内のフォルダーを一覧表示するよう Data Lake Store の REST エンドポイントに要求します。  これは、すべてが正しく構成されていることを簡単に確認する方法です。  Authorization ヘッダーの文字列 "Bearer" に、大文字の "B" があることが重要です。  Azure Portal の [Data Lake Store] ブレードの **[概要]** セクションで、ご利用の Data Lake Store の名前を確認できます。

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   正常な応答は次のようになります。

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. これで、Data Lake Store へのファイルのアップロードを試すことができます。  まず、アップロードするファイルを作成します。

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. PowerShell の `Invoke-WebRequest` を使用して、先ほど作成したフォルダーにファイルをアップロードするよう Data Lake Store の REST エンドポイントに要求します。  この要求では 2 つの手順が実行されます。  最初の手順では、要求を行うとファイルをアップロードする場所にリダイレクトされます。  2 番目の手順では、実際にファイルをアップロードします。  このチュートリアルとは異なる値を使用する場合は、フォルダーとファイルの名前を正しく設定するのを忘れないでください。 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   `$HdfsRedirectResponse` の値を確認すると、次の応答のような値になります。

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   リダイレクト エンドポイントに要求を送信してアップロードを完了します。

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   正常な応答は次のようになります。

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

その他の Data Lake Store ファイルシステムの API を使用すると、ファイルへの追加、ファイルのダウンロードなどができます。

お疲れさまでした。  VM の MSI を使用して、Data Lake Store ファイルシステムに認証されました。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- Data Lake Store の管理操作には、Azure Resource Manager を使用します。  VM の MSI を使用して、Resource Manager に認証する方法の詳細については、[Linux VM での管理対象サービス ID を使用した Resource Manager へのアクセス](msi-tutorial-linux-vm-access-arm.md)に関するページをご覧ください。
- 詳細については、「[Data Lake Store での Azure Active Directory を使用した認証](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)」をご覧ください。
- 詳細については、「[REST API を使用した Azure Data Lake Store に対するファイルシステム操作](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md)」または「[WebHDFS FileSystem APIs (WebHDFS ファイルシステム API)](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)」をご覧ください。
- 詳細については、「[Azure Data Lake Store のアクセス制御](~/articles/data-lake-store/data-lake-store-access-control.md)」をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
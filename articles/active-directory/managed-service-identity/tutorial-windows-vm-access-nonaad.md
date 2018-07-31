---
title: Windows VM マネージド サービス ID を使用して Azure Key Vault にアクセスする
description: Windows VM マネージド サービス ID を使用して Azure Key Vault にアクセスするプロセスについて説明するチュートリアルです。
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
ms.openlocfilehash: 81bab96b91bb71a91ea0b6046b16ef86c8d27061
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248063"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-key-vault"></a>チュートリアル: Windows VM マネージド サービス ID を使用して Azure Key Vault にアクセスする 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシンのマネージド サービス ID を有効にし、その ID を使用して Azure Key Vault にアクセスする方法について説明します。 ブートストラップとして機能する Key Vault により、クライアント アプリケーションは、Azure Active Directory (AD) で保護されていないリソースにシークレットを使用してアクセスできます。 管理対象サービス ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 

学習内容は次のとおりです。


> [!div class="checklist"]
> * Windows 仮想マシンで管理対象サービス ID を有効にする 
> * Key Vault に格納されているシークレットへ VM のアクセスを許可する 
> * VM ID を使用してアクセス トークンを取得して、Key Vault からシークレットを取得する 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このチュートリアルでは、新しい Windows VM を作成します。 既存の VM でマネージド サービス ID を有効にすることもできます。

1.  Azure Portal の左上隅にある **[リソースの作成]** ボタンをクリックします。
2.  **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3.  仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4.  ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5.  仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6.  VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

    ![イメージ テキスト](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>VM でマネージド サービス ID を有効にする 

仮想マシンのマネージド サービス ID を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 マネージド サービス ID を有効にすると、仮想マシンのマネージド ID を作成するよう Azure に指示が出されます。 マネージド サービス ID を有効にすると、内部では VM が Azure Active Directory に登録されて、そのマネージド ID が作成され、VM で ID が構成されます。

1.  マネージド サービス ID を有効にする**仮想マシン**を選択します。  
2.  左側のナビゲーション バーで、**[構成]** をクリックします。 
3.  **管理対象のサービス ID** が表示されます。 マネージド サービス ID を登録して有効にする場合は **[はい]** を選択し、無効にする場合は [いいえ] を選択します。 
4.  **[保存]** をクリックして構成を保存します。  

    ![イメージ テキスト](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Key Vault に格納されているシークレットへ VM のアクセスを許可する 
 
マネージド サービス ID を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。  ただし、すべての Azure サービスが Azure AD 認証をサポートしているわけではありません。 マネージド サービス ID をこれらのサービスとともに使用するには、Azure Key Vault にサービス資格情報を保存し、マネージド サービス ID を使用して Key Vault にアクセスして、資格情報を取得します。 

まず、Key Vault を作成し、VM の ID に Key Vault へのアクセスを許可する必要があります。   

1. 左側のナビゲーション バーの上部で、**[リソースの作成]** > **[セキュリティ + ID]** > **[Key Vault]** を選択します。  
2. 新しい Key Vault の**名前**を入力します。 
3. 以前に作成した VM と同じサブスクリプションおよびリソース グループで、Key Vault を見つけます。 
4. **[アクセス ポリシー]** を選択して **[新規追加]** ボタンをクリックします。 
5. テンプレートの [構成] で、**[シークレットの管理]** を選択します。 
6. **[プリンシパルの選択]** を選択し、検索フィールドに以前に作成した VM の名前を入力します。  結果一覧で VM を選択し、**[選択]** をクリックします。 
7. **[OK]** をクリックして新しいアクセス ポリシーの追加を終了し、**[OK]** をクリックしてアクセス ポリシーの選択を完了します。 
8. **[作成]** をクリックして Key Vault の作成を完了します。 

    ![イメージ テキスト](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


次に、Key Vault にシークレットを追加して、後で VM で実行されているコードを使用して、シークレットを取得できるようにします。 

1. **[すべてのリソース]** を選択し、作成した Key Vault を検索して選択します。 
2. **[シークレット]** を選択し、**[追加]** をクリックします。 
3. **[アップロード オプション]** から **[手動]** を選択します。 
4. シークレットの名前と値を指定します。  値は任意のものを指定できます。 
5. アクティブ化した日付と有効期限の日付をクリアのままにし、**[有効]** を **[はい]** のままにします。 
6. **[作成]** をクリックしてシークレットを作成します。 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>VM ID を使用してアクセス トークンを取得して、Key Vault からシークレットを取得する  

PowerShell 4.3.1 以上がインストールされていない場合、[最新バージョンをダウンロードしてインストールする](https://docs.microsoft.com/powershell/azure/overview)必要があります。

最初に、VM のマネージド サービス ID を使用して、Key Vault に対して認証するためのアクセス トークンを取得します。
 
1. ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** の **[接続]** をクリックします。
2. **Windows VM** を作成したときに追加した**ユーザー名**と**パスワード**を入力します。  
3. これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで PowerShell を開きます。  
4. PowerShell では、テナント上で Web 要求を呼び出し、VM の特定のポートでローカル ホストのトークンを取得します。  

    PowerShell は次のものを要求します。
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    次に、$response オブジェクト内で JavaScript オブジェクト表記 (JSON) 形式の文字列として格納されている完全な応答を抽出します。  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    次に、アクセス トークンを応答から抽出します。  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    最後に、PowerShell の Invoke-WebRequest コマンドを使用して、Key Vault で以前に作成したシークレットを取得し、Authorization ヘッダーにアクセス トークンを渡します。  Key Vault の **[概要]** ページの **[要点]** セクションにある Key Vault の URL が必要です。  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    応答は次のようになります。 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault からシークレットを取得した後は、名前とパスワードを必要とするサービスへの認証にそのシークレットを使用できます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Key Vault にアクセスするためのマネージド サービス ID の作成方法について説明します。  Azure Key Vault の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-whatis)

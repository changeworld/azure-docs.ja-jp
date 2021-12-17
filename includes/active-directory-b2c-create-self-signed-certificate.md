---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: kengaderdus
ms.openlocfilehash: 23fb85a1a9e796e9cdb7a2e0fbcde365c1d3724f
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133788"
---
証明書をまだ持っていない場合は、自己署名証明書を使用できます。 自己署名証明書は、証明機関 (CA) によって署名されていないセキュリティ証明書であり、CA によって署名された証明書のセキュリティ保証を提供するものではありません。 

# <a name="windows"></a>[Windows](#tab/windows)

Windows では、PowerShell の [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) コマンドレットを使用して証明書を生成します。

1. この PowerShell コマンドを実行して、自己署名証明書を生成します。 `contosowebapp.contoso.onmicrosoft.com` などのアプリケーションと Azure AD B2C のテナント名に合わせて `-Subject` 引数を変更します。 また、証明書に別の有効期限を指定するように `-NotAfter` 日付を調整することもできます。

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Windows コンピューターで、 **[ユーザー証明書の管理]** を検索して選択します 
1. **[証明書 - 現在のユーザー]** で、 **[個人用]**  >  **[証明書]** >*yourappname.yourtenant.onmicrosoft.com* を開きます。
1. 証明書を選択し、 **[アクション]**  >  **[すべてのタスク]**  >  **[エクスポート]** の順に選択します。
1. **[次へ]**  >  **[はい、秘密キーをエクスポートします]**  >  **[次へ]** を選択します。
1. **[エクスポート ファイルの形式]** の既定値を受け入れて、 **[次へ]** を選択します。
1. **[パスワード]** オプションを有効にして、証明書のパスワードを入力し、 **[次へ]** を選択します。
1. 証明書を保存する場所を指定するには、 **[参照]** を選択し、任意のディレクトリに移動します。 
1. **[名前を付けて保存]** ウィンドウで、 **[ファイル名]** を入力して、 **[保存]** を選択します。
1. **[次へ]** > **[完了]** の順に選択します。

Azure AD B2C で .pfx ファイルのパスワードを受け入れるには、Windows 証明書ストアのエクスポート ユーティリティで、AES256-SHA256 ではなく、TripleDES-SHA1 オプションを使用してパスワードを暗号化する必要があります。

# <a name="macos"></a>[macOS](#tab/macos)

macOS では、キーチェーン アクセスで[証明書アシスタント](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0)を使用して証明書を生成します。

1. [Mac 上でキーチェーン アクセスで自己署名証明書を作成する](https://support.apple.com/guide/keychain-access/kyca8916/mac)方法の手順に従ってください。
1. Mac 上のキーチェーン アクセス アプリで、作成した証明書を選択します。
1. **[ファイル]**  >  **[Export Items (項目のエクスポート)]** を選択します。
1. 証明書を保存するファイル名を選択します。 たとえば、**self-signed-certificate.p12** など。
1. **[ファイル形式]** には、**Personal Information Exchange (p12)** を選択します。
1. **[保存]** を選択します。
1. **[パスワード]** と **[Verify (確認)]** の欄にパスワードを入力します。
1. ファイル拡張子を .pfx に置き換えます。 たとえば、**self-signed-certificate.pfx** など。

---

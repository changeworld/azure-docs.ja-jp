---
title: 自己署名公開証明書を作成してアプリケーションを認証する | Azure
titleSuffix: Microsoft identity platform
description: 自己署名公開証明書を作成してアプリケーションを認証します。
services: active-directory
author: FaithOmbongi
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: ombongifaith
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: scenarios:getting-started
ms.openlocfilehash: 3cc084fe1b9df8a4ab4db5b926bb7b44646f17c2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030326"
---
# <a name="create-a-self-signed-public-certificate-to-authenticate-your-application"></a>自己署名公開証明書を作成してアプリケーションを認証する

Azure Active Directory (Azure AD) では、サービス プリンシパル用に、**パスワードベースの認証** (アプリ シークレット) と **証明書ベースの認証** という 2 種類の認証がサポートされています。 アプリ シークレットは Azure portal で簡単に作成できますが、アプリケーションでは証明書を使用することをお勧めします。

テストでは、証明機関 (CA) で署名された証明書ではなく、自己署名公開証明書を使用できます。 この記事では、Windows PowerShell を使用して自己署名証明書を作成およびエクスポートする方法について説明します。

> [!CAUTION]
> 自己署名証明書は、運用環境ではなく開発環境でのみ使用することをお勧めします。

証明書では、さまざまなパラメーターを構成します。 たとえば、暗号およびハッシュ アルゴリズム、証明書の有効期間、ドメイン名などです。 次に、アプリケーションのニーズに応じて秘密キーを含めるか含めずに、証明書をエクスポートします。 

認証セッションを開始するアプリケーションでは秘密キーが必要ですが、認証を確認するアプリケーションでは公開キーが必要です。 そのため、PowerShell デスクトップ アプリから Azure AD に対して認証を行っている場合は、公開キー (`.cer` ファイル) のみをエクスポートして Azure portal にアップロードします。 PowerShell アプリではローカル証明書ストアの秘密キーを使用して認証を開始し、Microsoft Graph のアクセス トークンを取得します。

アプリケーションが、Azure Automation などの別のマシンから実行されている場合もあります。 このシナリオでは、公開キーと秘密キーのペアをローカル証明書ストアからエクスポートして、公開キーは Azure portal に、秘密キー (`.pfx` ファイル) は Azure Automation にアップロードします。 Azure Automation で実行されているアプリケーションでは、秘密キーを使用して認証を開始し、Microsoft Graph のアクセス トークンを取得します。

この記事では、`New-SelfSignedCertificate` PowerShell コマンドレットを使用して自己署名証明書を作成し、`Export-Certificate` コマンドレットを使用して、簡単にアクセスできる場所にエクスポートします。 これらのコマンドレットは、最新バージョンの Windows (Windows 8.1 以降、および Windows Server 2012R2 以降) に組み込まれています。 自己署名証明書には、以下の構成があります。

+ 2,048 ビットのキー長。 より長い値がサポートされていますが、セキュリティとパフォーマンスの組み合わせが最適な 2,048 ビットのサイズを強くお勧めします。
+ RSA 暗号アルゴリズムを使用します。 Azure AD では、現在、RSA のみがサポートされています。
+ 証明書は、SHA256 ハッシュ アルゴリズムで署名されています。 Azure AD では、SHA384 および SHA512 ハッシュ アルゴリズムで署名された証明書もサポートされています。
+ 証明書は 1 年間のみ有効です。
+ 証明書は、クライアントとサーバーの両方の認証で使用できるようにサポートされています。

> [!NOTE]
> 証明書の開始日と有効期限日、およびその他のプロパティをカスタマイズするには、[`New-SelfSignedCertificate` のリファレンス](/powershell/module/pki/new-selfsignedcertificate?view=windowsserver2019-ps&preserve-view=true)を参照してください。


## <a name="option-1--create-and-export-your-public-certificate-without-a-private-key"></a>オプション 1: 秘密キーがない公開証明書を作成してエクスポートする

この方法を使用して作成した証明書を使用して、自分のマシンで実行されているアプリケーションから認証を行います。 たとえば、Windows PowerShell から認証します。

管理者特権の PowerShell プロンプトで次のコマンドを実行し、PowerShell コンソール セッションを開いたままにします。 `{certificateName}` を、証明書に付ける名前に置き換えます。

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

前のコマンドの **$cert** 変数には現在のセッションの証明書が格納されるため、それをエクスポートできます。 次のコマンドでは、証明書を `.cer` 形式でエクスポートします。 また、`.pem`、`.crt` など、Azure portal でサポートされている他の形式でエクスポートすることもできます。

```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

これで、証明書を Azure portal にアップロードする準備ができました。 アップロードが完了したら、アプリケーションの認証に使用する証明書の拇印を取得します。


## <a name="option-2-create-and-export-your-public-certificate-with-its-private-key"></a>オプション 2: 秘密キーがある公開証明書を作成してエクスポートする

Azure Automation のように、アプリケーションが別のマシンまたはクラウドから実行される場合は、このオプションを使用して証明書とその秘密キーを作成します。

管理者特権の PowerShell プロンプトで次のコマンドを実行し、PowerShell コンソール セッションを開いたままにします。 `{certificateName}` を、証明書に付ける名前に置き換えます。

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

前のコマンドの **$cert** 変数には現在のセッションの証明書が格納されるため、それをエクスポートできます。 次のコマンドでは、証明書を `.cer` 形式でエクスポートします。 また、`.pem`、`.crt` など、Azure portal でサポートされている他の形式でエクスポートすることもできます。


```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

引き続き同じセッションで、証明書の秘密キーのパスワードを作成し、変数に保存します。 次のコマンドで、`{myPassword}` を、証明書の秘密キーを保護するために使用するパスワードに置き換えます。

```powershell

$mypwd = ConvertTo-SecureString -String "{myPassword}" -Force -AsPlainText  ## Replace {myPassword}

```

ここで、`$mypwd` 変数に格納したパスワードを使用して、秘密キーを保護し、エクスポートします。

```powershell

Export-PfxCertificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{privateKeyName}.pfx" -Password $mypwd   ## Specify your preferred location and replace {privateKeyName}

```

これで、証明書 (`.cer` ファイル) を Azure portal にアップロードする準備ができました。 また、暗号化されて他の関係者には読み取ることができない秘密キー (`.pfx` ファイル) もあります。 アップロードが完了したら、アプリケーションの認証に使用する証明書の拇印を取得します。


## <a name="optional-task-delete-the-certificate-from-the-keystore"></a>省略可能なタスク: キーストアから証明書を削除する。

オプション 2 を使用して証明書を作成した場合は、個人用ストアからキー ペアを削除できます。 まず、次のコマンドを実行して、証明書の拇印を取得します。

```powershell

Get-ChildItem -Path "Cert:\CurrentUser\My" | Where-Object {$_.Subject -Match "{certificateName}"} | Select-Object Thumbprint, FriendlyName    ## Replace {privateKeyName} with the name you gave your certificate

```

次に、表示されている拇印をコピーし、それを使用して証明書とその秘密キーを削除します。

```powershell

Remove-Item -Path Cert:\CurrentUser\My\{pasteTheCertificateThumbprintHere} -DeleteKey

```

### <a name="know-your-certificate-expiry-date"></a>証明書の有効期限を確認する

上の手順に従って作成した自己署名証明書には、有効期限があります。 Azure portal の **[アプリの登録]** セクションで、 **[証明書とシークレット]** 画面に証明書の有効期限が表示されます。 Azure Automation を使用している場合は、Automation アカウントの **[証明書]** 画面に証明書の有効期限が表示されます。 前の手順に従って、新しい自己署名証明書を作成します。

## <a name="next-steps"></a>次のステップ

[Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](../manage-apps/manage-certificates-for-federated-single-sign-on.md)

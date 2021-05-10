---
title: AD FS 証明書の緊急ローテーション |Microsoft Docs
description: この記事では、AD FS 証明書をただちに失効させて、更新する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612954"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>AD FS 証明書の緊急ローテーション
AD FS 証明書をただちにローテーションする必要がある場合は、このセクションで後述する手順に従うことができます。

> [!IMPORTANT]
> AD FS 環境で下記の手順を実行すると、古い証明書がただちに失効します。  これはすぐに行われるため、通常フェデレーション パートナーが新しい証明書を使用するために与えられる通常の時間が回避されます。 これにより、新しい証明書を使用するように信頼が更新されるため、サービスの停止が発生することがあります。  これは、すべてのフェデレーション パートナーが新しい証明書を持つと解決されます。

> [!NOTE]
> Microsoft では、証明書を保護し、セキュリティで保護するために、ハードウェア セキュリティ モジュール (HSM) を使用することを強く推奨します。
> 詳細については、AD FS をセキュリティ保護するためのベスト プラクティスの「[ハードウェア セキュリティ モジュール](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm)」を参照してください。

## <a name="determine-your-token-signing-certificate-thumbprint"></a>トークン署名証明書の拇印を特定する
現在 AD FS で使用している古いトークン署名証明書を失効させるには、トークン署名証明書の拇印を特定する必要があります。  これを行うには、次の手順を使用します。

 1. Microsoft オンライン サービスに接続します。`PS C:\>Connect-MsolService`
 2. オンプレミスとクラウドの両方のトークン署名証明書の拇印と有効期限を文書化します。
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  拇印をコピーします。  これは後で既存の証明書を削除するために使用します。

また、AD FS 管理を使用して、[サービス]、[証明書] の順に移動し、証明書を右クリックして [証明書の表示] を選択し、[詳細] を選択して、拇印を取得することもできます。 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>AD FS によって証明書を自動的に更新させるかどうかを決定する
既定では、AD FS は、初期構成時にも、証明書の有効期限が近づいたときにも、トークン署名証明書とトークン暗号化解除証明書を自動的に生成するように構成されます。

Windows PowerShell コマンド `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` を実行できます。

AutoCertificateRollover プロパティは、AD FS がトークン署名証明書とトークン暗号化解除証明書を自動的に更新するように構成されているかどうかを示します。  AutoCertificateRollover が TRUE に設定されている場合は、下の「AutoCertificateRollover が TRUE に設定されている場合の新しい自己署名証明書の生成」に説明されている手順に従います。  AutoCertificateRollover が FALSE に設定されている場合は、下の「AutoCertificateRollover が FALSE に設定されている場合の新しい証明書の手動での生成」に説明されている手順に従います


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>AutoCertificateRollover が TRUE に設定されている場合の新しい自己署名証明書の生成
このセクションでは、**2 つ** のトークン署名証明書を作成します。  1 つ目は、 **-urgent** フラグを使用し、これにより、現在のプライマリ証明書がただちに置き換えられます。  2 つ目は、セカンダリ証明書として使用されます。  

>[!IMPORTANT]
>2 つの証明書を作成する理由は、Azure で前の証明書に関する情報が保持されるためです。  2 つ目の証明書を作成することで、Azure に古い証明書に関する情報を除去させ、2 つ目の証明書に関する情報に置き換えさせることができます。
>
>2 つ目の証明書を作成して、それによって Azure を更新しない場合、古いトークン署名証明書でユーザーを認証できてしまう可能性があります。

新しいトークン署名証明書を生成するには、次の手順を実行します。

 1. プライマリ AD FS サーバーにログオンしていることを確認します。
 2. Windows PowerShell を管理者として開きます。 
 3. AutoCertificateRollover が True に設定されていることを確認します。
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. 新しいトークン署名証明書を生成するには: `Update-ADFSCertificate –CertificateType token-signing -Urgent`
 5. 次のコマンドを再実行して更新を確認します。`Get-ADFSCertificate –CertificateType token-signing`
 6. 次に、2 つ目のトークン署名証明書を生成します。`Update-ADFSCertificate –CertificateType token-signing`
 7. 次のコマンドを再度実行して更新を確認できます。`Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>AutoCertificateRollover が FALSE に設定されている場合の新しい証明書の手動による生成
自動的に生成された既定の自己署名トークン署名証明書とトークン暗号化解除証明書を使用していない場合は、これらの証明書を手動で更新して構成する必要があります。  これには、2 つの新しいトークン署名証明書の作成とそれらのインポートが含まれます。  次に、一方をプライマリに昇格させ、古い証明書を失効させて、2 つ目の証明書をセカンダリ証明書として構成します。

まず、証明機関から 2 つの新しい証明書を取得し、各フェデレーション サーバー上のローカル コンピューターの個人証明書ストアにそれらをインポートする必要があります。 手順については、「[証明書のインポート](https://technet.microsoft.com/library/cc754489.aspx)」の記事を参照してください。

>[!IMPORTANT]
>2 つの証明書を作成する理由は、Azure で前の証明書に関する情報が保持されるためです。  2 つ目の証明書を作成することで、Azure に古い証明書に関する情報を除去させ、2 つ目の証明書に関する情報に置き換えさせることができます。
>
>2 つ目の証明書を作成して、それによって Azure を更新しない場合、古いトークン署名証明書でユーザーを認証できてしまう可能性があります。

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>新しい証明書をセカンダリ証明書として構成するには
次に、1 つの証明書をセカンダリの AD FS トークン署名証明書または暗号化解除証明書として構成してから、それをプライマリに昇格させる必要があります

1. 証明書のインポートが完了したら、 **AD FS の管理** コンソールを開きます。
2. [**サービス**] を展開し、[**証明書**] を選択します。
3. [操作] ウィンドウで、 **[トークン - 署名証明書の追加]** をクリックします。
4. 表示された証明書一覧から新しい証明書を選択し、[OK] をクリックします。

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>新しい証明書をセカンダリからプライマリへ昇格するには
新しい証明書が AD FS にインポートされ、構成されたので、プライマリ証明書として設定する必要があります。
1. **AD FS の管理** コンソールを開きます。
2. [**サービス**] を展開し、[**証明書**] を選択します。
3. セカンダリ トークン署名証明書をクリックします。
4. [**アクション**] ペインで、[**プライマリに設定**] をクリックします。 確認を求められたら [はい] をクリックします。
5. 新しい証明書をプライマリ証明書として昇格させたら、古い証明書はまだ使用できるため、削除する必要があります。 下の「[古い証明書の削除](#remove-your-old-certificates)」セクションを参照してください。  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>2 つ目の証明書をセカンダリ証明書として構成するには
1 つ目の証明書を追加し、それをプライマリにして、古い証明書を削除したので、2 つ目の証明書をインポートします。  次に、証明書をセカンダリ AD FS トークン署名証明書として構成する必要があります

1. 証明書のインポートが完了したら、 **AD FS の管理** コンソールを開きます。
2. [**サービス**] を展開し、[**証明書**] を選択します。
3. [操作] ウィンドウで、 **[トークン - 署名証明書の追加]** をクリックします。
4. 表示された証明書一覧から新しい証明書を選択し、[OK] をクリックします。

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>新しいトークン署名証明書で Azure AD を更新する
Windows PowerShell 用 Microsoft Azure Active Directory モジュールを開きます。 または、Windows PowerShell を開き、コマンド `Import-Module msonline` を実行します

コマンド `Connect-MsolService` を実行して Azure AD に接続し、次に全体管理者の資格情報を入力します。

>[!Note]
> プライマリ フェデレーション サーバーでないコンピューターでこれらのコマンドを実行する場合は、まずコマンド `Set-MsolADFSContext –Computer <servername>` を入力します。 <servername> を AD FS サーバーの名前に置き換えます。 入力を求められたら、AD FS サーバーの管理者資格情報を入力します。

オプションで、Azure AD の現在の証明書情報を確認して、更新が必要であるかどうかを確認します。 これを行うには、コマンド `Get-MsolFederationProperty` を実行します。 入力が求められたら、フェデレーション ドメインの名前を入力します。

Azure AD の証明書情報を更新するには、コマンド`Update-MsolFederatedDomain` を実行して、入力が求められたら、ドメイン名を入力します。

>[!Note]
> このコマンドの実行中にエラーが発生した場合は、コマンド `Update-MsolFederatedDomain –SupportMultipleDomain` を実行し、入力が求められたら、ドメイン名を入力します。

## <a name="replace-ssl-certificates"></a>SSL 証明書の置き換え
セキュリティ侵害のためにトークン署名証明書を置き換える必要がある場合は、AD FS と WAP サーバーの SSL 証明書も失効させて置き換える必要があります。  

SSL 証明書の失効は、証明書を発行した証明機関 (CA) で行う必要があります。  これらの証明書は、多くの場合、GoDaddy などのサード パーティ プロバイダーによって発行されます。  例については、GoDaddy の SSL 証明書ヘルプの証明書の失効に関するページを参照してください。  詳細については、「[証明書の失効のしくみ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN)」を参照してください。

古い SSL 証明書が失効し、新しいものが発行されたら、SSL 証明書を置き換えることができます。 詳細については、「[AD FS の SSL 証明書の置き換え](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)」を参照してください。


## <a name="remove-your-old-certificates"></a>古い証明書の削除
古い証明書を置き換えたら、古い証明書はまだ使用できるため、削除する必要があります。 . これを行うには、次の手順に従います。  これを行うには、次の手順に従います。

1. プライマリ AD FS サーバーにログオンしていることを確認します。
2. Windows PowerShell を管理者として開きます。 
4. 古いトークン署名証明書を削除するには: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>`

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>フェデレーション メタデータを使用できるフェデレーション パートナーの更新
新しいトークン署名証明書またはトークン暗号化解除証明書を更新して構成する場合は、すべてのフェデレーション パートナー (証明書利用者の信頼とクレーム プロバイダーの信頼によって AD FS で表されるリソース組織またはアカウント組織パートナー) が新しい証明書を選択していることを確認する必要があります。

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>フェデレーション メタデータを使用できないフェデレーション パートナーの更新
フェデレーション パートナーがフェデレーション メタデータを使用できない場合は、新しいトークン署名またはトークン暗号化解除証明書の公開キーをパートナーに手動で送信する必要があります。 新しい証明書公開キー (.cer ファイルまたはチェーン全体を含める場合は . p7b ファイル) を、すべてのリソース組織またはアカウント組織パートナー (AD FS で証明書利用者の信頼とクレーム プロバイダーの信頼によって表される) に送信します。 新しい証明書を信頼するために、パートナー側で変更を実装してもらう必要があります。



## <a name="revoke-refresh-tokens-via-powershell"></a>PowerShell による更新トークンの失効
ここで、更新トークンを持っている可能性のあるユーザーの更新トークンを失効させ、そのユーザーに再ログオンと新しいトークンの取得を強制する必要があります。  これにより、電話、現在の Web メールセッション、およびトークンと更新トークンを使用しているその他の項目から、ユーザーがログアウトされます。  詳細については、[こちら](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true)を参照してください。さらに、[Azure Active Directory でユーザー アクセスを取り消す](../../active-directory/enterprise-users/users-revoke-access.md)方法を参照することもできます。

## <a name="next-steps"></a>次のステップ

- [Windows Server 2016 の AD FS と WAP で SSL 証明書を管理する](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [AD FS のトークン署名証明書とトークン暗号化解除証明書の取得と構成](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Microsoft 365 および Azure Active Directory 用のフェデレーション証明書の更新](how-to-connect-fed-o365-certs.md)




















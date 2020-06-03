---
title: Azure Active Directory の代替ログイン ID としてメールでサインインする
description: 代替ログイン ID としてメール アドレスを使用して Azure Active Directory にサインインする (プレビュー) ようにユーザーを構成して有効にする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837311"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>代替ログイン ID としてメールを使用して Azure にサインインする (プレビュー)

多くの組織では、ユーザーがオンプレミス ディレクトリ環境と同じ資格情報を使用して Azure にサインインできるようにしたいと考えています。 ハイブリッド認証と呼ばれるこの方法では、ユーザーは 1 組の資格情報を記憶しておくだけでよくなります。

一部の組織では、次の理由によりハイブリッド認証に移行していません。

* 既定で、Azure Active Directory (Azure AD) のユーザー プリンシパル名 (UPN) が、オンプレミス ディレクトリと同じ UPN に設定されています。
* Azure AD UPN を変更すると、オンプレミス環境と Azure 環境の間で不一致が生じ、特定のアプリケーションやサービスで問題が発生する可能性があります。
* ビジネスまたはコンプライアンス上の理由により、組織では、Azure へのサインインにオンプレミスの UPN を使用したくないと考えています。

ハイブリッド認証への移行を支援するために、ユーザーが代替ログイン ID として確認済みドメイン内のメールで Azure にサインインできるように Azure AD を構成できます。 たとえば、*Contoso* が *Fabrikam* にブランド変更した場合、従来の `balas@contoso.com` UPN でのサインインを続行するのではなく、代替ログイン ID としてメールを使用できるようになりました。 アプリケーションまたはサービスにアクセスするために、ユーザーは、割り当てられたメール (`balas@fabrikam.com` など) を使用して Azure にサインインします。

|     |
| --- |
| 代替ログイン ID としてメールを使用した Azure AD へのサインインは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD サインイン方法の概要

ユーザープリンシパル名 (UPN) は、オンプレミス ディレクトリと Azure AD でのユーザー アカウントの一意の識別子です。 ディレクトリ内の各ユーザー アカウントは、`balas@contoso.com` などの UPN によって表されます。 既定では、オンプレミスの Active Directory ドメイン サービス (AD DS) 環境を Azure AD と同期する場合、Azure AD UPN はオンプレミスの UPN と一致するように設定されます。

多くの組織では、オンプレミスの UPN と Azure AD UPN を一致するように設定することは問題ありません。 ユーザーは、Azure アプリケーションとサービスにサインインするときに、Azure AD UPN を使用します。 ただし、ビジネス ポリシーやユーザー エクスペリエンスの問題のために、サインインに一致する UPN を使用できない組織もあります。

Azure AD で一致する UPN を使用できない組織には、いくつかのオプションがあります。

* 1 つの方法は、ビジネス ニーズに基づいて、Azure AD UPN を `balas@fabrikam.com` のように別のものに設定することです。
    * ただし、すべてのアプリケーションとサービスが、オンプレミス UPN と Azure AD UPN に異なる値を使用することに対応できるわけではありません。
* より適切な方法は、Azure AD とオンプレミスの UPN を同じ値に設定し、ユーザーが代替ログイン ID としてメールを使用して Azure にサインインできるように Azure AD を構成するというものです。

代替ログイン ID としてメールを使用する場合、ユーザーは引き続き UPN を入力して Azure にサインインできますが、メールを使用してサインインすることもできます。 これをサポートするには、オンプレミス ディレクトリのユーザーの *ProxyAddresses* 属性でメール アドレスを定義します。 この *ProxyAddress* 属性は 1 つ以上のメール アドレスをサポートします。

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>サインインのメール アドレスを Azure AD に同期する

従来の Active Directory ドメイン サービス (AD DS) 認証または Active Directory フェデレーションサービス (AD FS) 認証は、ネットワーク上で直接行われ、AD DS インフラストラクチャによって処理されます。 ハイブリッド認証では、ユーザーは代わりに Azure AD に直接サインインすることができます。

このハイブリッド認証アプローチをサポートするには、オンプレミスの AD DS 環境を、[Azure AD Connect][azure-ad-connect] を使用して Azure AD に同期させ、パスワード ハッシュの同期 (PHS) またはパススルー認証 (PTA) を使用するように構成します。

どちらの構成オプションでも、ユーザーはユーザー名とパスワードを Azure AD に送信し、ここで資格情報が検証され、チケットが発行されます。 ユーザーが Azure AD にサインインすると、組織が AD FS インフラストラクチャをホストし管理する必要性はなくなります。

![パスワード ハッシュ同期を使用した Azure AD ハイブリッド ID の図](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![パススルー認証を使用した Azure AD ハイブリッド ID の図](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Azure AD Connect によって自動的に同期されるユーザー属性の 1 つが *ProxyAddresses* です。 オンプレミスの AD DS 環境で *ProxyAddresses* 属性の一部として定義されているメール アドレスをユーザーが持っている場合は、Azure AD に自動的に同期されます。 このメール アドレスは、直接 Azure AD サインイン プロセスで、代替ログイン ID として使用できます。

> [!IMPORTANT]
> テナントの確認済みドメインのメールのみが Azure AD に同期されます。 それぞれの Azure AD テナントには、1 つ以上の確認済みドメインがあり、これらは所有権が実証され、テナントに一意にバインドされます。
>
> 詳細については、[Azure AD でのカスタム ドメイン名の追加と確認][verify-domain]に関するページをご覧ください。

詳細については、「[Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する][hybrid-auth-methods]」をご覧ください。

## <a name="enable-user-sign-in-with-an-email-address"></a>メール アドレスを使用してユーザーのサインインを有効にする

*ProxyAddresses* 属性を適用したユーザーが Azure AD Connect を使用して Azure AD に同期された後、ユーザーがテナントの代替ログイン ID としてメールを使用してサインインできるようにする機能を有効にする必要があります。 この機能は、UPN 値に対してだけでなく、メール アドレスの *ProxyAddresses* 値に対してサインイン名を確認するように、Azure AD ログイン サーバーに指示します。

現在、プレビュー期間中は、PowerShell を使用して、代替ログイン ID 機能としてメールを使用したサインインのみを有効にすることができます。 次の手順を完了するには、*テナント管理者*権限が必要です。

1. 管理者として PowerShell セッションを開き、[Install-Module][Install-Module] コマンドレットを使用して *AzureADPreview* モジュールをインストールします。

    ```powershell
    Install-Module AzureADPreview
    ```

    メッセージが表示されたら、 **[Y]** を選択して NuGet をインストールするか、信頼されていないリポジトリからインストールします。

1. [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して *テナント管理者*として Azure AD テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

    このコマンドは、アカウント、環境、およびテナント ID に関する情報を返します。

1. 次のように [Get-AzureADPolicy][Get-AzureADPolicy] コマンドレットを使用して、*HomeRealmDiscoveryPolicy* ポリシーがテナントに 既に存在しているかどうかを確認します。

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. 現在構成されているポリシーがない場合、コマンドは何も返しません。 ポリシーが返された場合は、この手順をスキップし、次の手順に進んで、既存のポリシーを更新します。

    *HomeRealmDiscoveryPolicy* ポリシーをテナントに追加するには、次の例に示すように、[New-AzureADPolicy][New-AzureADPolicy] コマンドレットを使用し、*AlternateIdLogin* 属性を *"Enabled": true* に設定します。

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    ポリシーが正常に作成されると、次の出力例に示すように、コマンドによってポリシー ID が返されます。

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 既にポリシーが構成されている場合は、次のポリシー出力の例に示すように、 *AlternateIdLogin*  属性が有効になっているかどうかを確認します。

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    ポリシーが存在していても、*AlternateIdLogin* 属性が存在していないか有効になっていない場合、または他の属性が保持したいと考えているポリシーに存在している場合は、[Set-AzureADPolicy][Set-AzureADPolicy] コマンドレットを使用して既存のポリシーを更新します。

    > [!IMPORTANT]
    > ポリシーを更新するときには、必ず古い設定と新しい  *AlternateIdLogin* 属性を含めます。

    次の例では、 *AlternateIdLogin* 属性を追加し、既に設定されている可能性のある  *AllowCloudPasswordValidation* 属性を保持します。

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    更新されたポリシーによって変更が表示され、*AlternateIdLogin* 属性が有効になっていることを確認します。

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>メールでユーザー サインインをテストする

ユーザーがメールでサインインできるかどうかをテストするには、[https://myprofile.microsoft.com][my-profile] を参照し、UPN (`balas@contoso.com` など) ではなくそのメール アドレス (`balas@fabrikam.com` など) に基づいてユーザー アカウントでサインインできます。 サインイン エクスペリエンスは、UPN ベースのサインイン イベントの場合と同じ外観になります。

## <a name="troubleshoot"></a>トラブルシューティング

ユーザーがメール アドレスを使用したサインイン イベントに問題を抱えている場合は、次のトラブルシューティングの手順を確認してください。

1. オンプレミスの AD DS 環境で、*ProxyAddresses* 属性に対してユーザー アカウントのメール アドレスが設定されていることを確認します。
1. Azure AD Connect が構成されていること、およびオンプレミスの AD DS 環境から Azure AD にユーザー アカウントが正常に同期されていることを確認します。
1. Azure AD *HomeRealmDiscoveryPolicy* ポリシーの *AlternateIdLogin* 属性が *"Enabled": true* に設定されていることを確認します。

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>次のステップ

Azure AD アプリ プロキシや Azure AD Domain Services などのハイブリッド ID の詳細については、[オンプレミス ワークロードのアクセスおよび管理の Azure AD ハイブリッド ID][hybrid-overview] に関するページをご覧ください。

ハイブリッド ID 操作の詳細については、[パスワード ハッシュ同期方法][phs-overview]または[パススルー認証][pta-overview]の同期をご覧ください。

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com

---
title: B2B ユーザーにオンプレミスのアプリへのアクセス許可する - Azure AD
description: Azure AD B2B Collaboration を使用してクラウド B2B ユーザーにオンプレミスのアプリへのアクセスを許可する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/05/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ebaa3c9b6a931a01eb65a28cd48cf29e74c13b3
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892402"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する

Azure Active Directory (Azure AD) B2B コラボレーション機能を使用して取引先組織のゲスト ユーザーを Azure AD に招待している組織が、これらの B2B ユーザーにオンプレミスのアプリケーションへのアクセスを提供できるようになりました。 これらのオンプレミス アプリは、SAML ベースの認証または統合 Windows 認証 (IWA) を Kerberos の制約付き委任 (KCD) と共に使用できます。

## <a name="access-to-saml-apps"></a>SAML アプリケーションへのアクセス

オンプレミスのアプリで SAML による認証を使用する場合は、Azure portal で Azure AD アプリケーション プロキシを使用して、Azure AD B2B のコラボレーション ユーザーにもそれらのアプリが利用できるように、簡単に設定できます。

次の手順を実行する必要があります。

- アプリケーション プロキシを有効にしてコネクターをインストールする。 手順については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../app-proxy/application-proxy-add-on-premises-application.md)」を参照してください。
- 「[アプリケーション プロキシを使用したオンプレミスのアプリケーションに対する SAML シングル サインオン](../app-proxy/application-proxy-configure-single-sign-on-on-premises-apps.md)」の説明に従って、Azure AD アプリケーション プロキシで、SAML を利用するオンプレミスのアプリを公開します。
- Azure AD B2B User を SAML Application に割り当てます。

上の手順を完了したら、アプリが動作します。 Azure AD B2B のアクセスをテストするには
1.  ブラウザーを開き、アプリを発行したときに作成した外部 URL に移動します。
2.  アプリに割り当てた Azure AD B2B アカウントでサインインします。 アプリを開き、シングル サインオンでアクセスできます。

## <a name="access-to-iwa-and-kcd-apps"></a>IWA および KCD アプリへのアクセス

B2B ユーザーに、統合 Windows 認証と Kerberos の制約付き委任を使用してセキュリティで保護されたオンプレミス アプリケーションへのアクセスを提供するには、次のコンポーネントが必要です。

- **Azure AD アプリケーション プロキシを介した認証**。 B2B ユーザーは、オンプレミス アプリケーションに対して認証できる必要があります。 これを行うには、Azure AD アプリケーション プロキシを介してオンプレミス アプリを発行する必要があります。 詳細については、[アプリケーション プロキシを使用したリモート アクセスを行うためのオンプレミス アプリケーションの追加に関するチュートリアル](../app-proxy/application-proxy-add-on-premises-application.md)を参照してください。
- **オンプレミス ディレクトリの B2B ユーザー オブジェクトを介した承認**。 アプリケーションは、ユーザー アクセス チェックを実行し、正しいリソースへのアクセス権を付与できる必要があります。 IWA と KCD がこの承認を完了するには、オンプレミスの Windows Server Active Directory 内のユーザー オブジェクトが必要です。 「[KCD を使ったシングル サインオンのしくみ](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)」で説明されているように、アプリケーション プロキシはこのユーザー オブジェクトを使用してユーザーを偽装し、アプリに対する Kerberos トークンを取得する必要があります。 

   > [!NOTE]
   > Azure AD アプリケーション プロキシを構成する場合は、統合 Windows 認証 (IWA) のシングル サインオン構成で **[委任されたログオン ID]** が **[ユーザー プリンシパル名]** (既定値) に設定されていることを確認してください。

   B2B ユーザーのシナリオでは、オンプレミス ディレクトリでの承認に必要なゲスト ユーザー オブジェクトを作成できる方法が 2 つあります。

   - Microsoft Identity Manager (MIM) と Microsoft Graph 用 MIM 管理エージェント。
   - PowerShell スクリプトは、MIM を必要としない、より軽量な解決策です。

次の図は、Azure AD アプリケーション プロキシと、オンプレミス ディレクトリ内の B2B ユーザー オブジェクトの生成を連携して、B2B ユーザーにオンプレミス IWA および KCD アプリへのアクセス権を付与する方法の概要を示しています。 番号が付いた手順については、図の下の詳細な説明を参照してください。

![MIM および B2B スクリプトの解決策の図](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  パートナー組織のユーザー (Fabrikam テナント) が Contoso テナントに招待されます。
2.  ゲスト ユーザー オブジェクトが Contoso テナントに作成されます (たとえば、UPN が guest_fabrikam.com#EXT#@contoso.onmicrosoft.com のユーザー オブジェクト)。
3.  MIMO または B2B PowerShell スクリプトを介して Fabrikam ゲストが Contoso からインポートされます。
4.  Fabrikam ゲスト ユーザー オブジェクト (Guest#EXT#) の表現つまり "フットプリント" が、MIM または B2B PowerShell スクリプトを介してオンプレミス ディレクトリの Contoso.com に作成されます。
5.  ゲスト ユーザーがオンプレミスのアプリケーションの app.contoso.com にアクセスします。
6.  認証要求が、Kerberos の制約付き委任を使用して Application Proxy を介して承認されます。 
7.  ゲスト ユーザー オブジェクトがローカルに存在するため、認証は成功します。

### <a name="lifecycle-management-policies"></a>ライフサイクル管理ポリシー

ライフサイクル管理ポリシーを使用して、オンプレミス B2B ユーザー オブジェクトを管理できます。 次に例を示します。

- アプリケーション プロキシ認証中に多要素認証 (MFA) が使用されるようにゲスト ユーザーの MFA ポリシーを設定することができます。 詳細については、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」を参照してください。
- クラウド B2B ユーザーに対して実行されるスポンサー、アクセス レビュー、アカウントの検証などが、オンプレミス ユーザーに適用されます。 たとえば、ライフサイクル管理ポリシーを使用してクラウド ユーザーが削除された場合、MIM 同期または Azure AD Connect 同期によってオンプレミス ユーザーも削除されます。詳細については、「[Azure AD のアクセス レビューによるゲスト アクセスの管理](../governance/manage-guest-access-with-access-reviews.md)」を参照してください。

### <a name="create-b2b-guest-user-objects-through-mim"></a>MIM を介した B2B ゲスト ユーザー オブジェクトの作成

MIM 2016 Service Pack 1 および Microsoft Graph の MIM 管理エージェントを使用してオンプレミス ディレクトリにゲスト ユーザー オブジェクトを作成する方法については、「[Azure AD business-to-business (B2B) collaboration with Microsoft Identity Manager(MIM) 2016 SP1 with Azure Application Proxy (Public Preview)](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)」(Azure アプリケーション プロキシを使用した Azure AD B2B (Business-to-Business) と Microsoft Identity Manager (MIM) 2016 SP1 のコラボレーション) をご覧ください。

## <a name="license-considerations"></a>ライセンスに関する考慮事項

オンプレミス アプリにアクセスする外部ゲスト ユーザーに対して、正しいクライアント アクセス ライセンス (CAL) があることを確認してください。 詳細については、「[クライアント アクセス ライセンスと マネジメント ライセンス](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)」の「エクスターナル コネクタ」セクションを参照してください。 具体的なライセンス要件については、マイクロソフトの担当者または地域の販売代理店にお問い合わせください。

## <a name="next-steps"></a>次のステップ

- 「[ハイブリッド組織向けの Azure Active Directory B2B コラボレーション](hybrid-organizations.md)」も参照してください。

- Azure AD Connect の概要については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」を参照してください。

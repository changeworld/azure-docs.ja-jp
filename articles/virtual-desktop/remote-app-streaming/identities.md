---
title: Azure Virtual Desktop でマネージド ID を設定する - Azure
description: Azure AD、Azure AD DS、または AD DS を使用して Azure Virtual Desktop で顧客のマネージド ID を設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 98dcd68e62f04eaf9ae439389cb31e281cdb2f69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778950"
---
# <a name="set-up-managed-identities"></a>マネージド ID を設定する

現在、Azure Virtual Desktop では外部プロファイルまたは "ID" がサポートされていないため、ユーザーは、ホストされているアプリに自身の会社の資格情報を使用してアクセスすることができません。 代わりに、リモート アプリのストリーミングに使用する Active Directory ドメインでユーザーの ID を作成し、ユーザー オブジェクトを関連付けられた Azure Active Directory (Azure AD) テナントに同期する必要があります。

この記事では、ユーザー ID を管理して、セキュリティで保護された環境を顧客に提供する方法について説明します。 また、ID を構成するさまざまな要素についても説明します。

## <a name="requirements"></a>要件

作成する ID は、これらのガイドラインに従う必要があります。

- ID は[ハイブリッド ID](../../active-directory/hybrid/whatis-hybrid-identity.md) である必要があります。これは、ID が [Active Directory (AD)](/previous-versions/windows/it-pro/windows-server-2003/cc781408(v=ws.10)) と [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) の両方に存在することを意味します。 これらの ID は、[Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/active-directory-domain-services) または [Azure Active Directory Domain Services (Azure AD DS)](https://azure.microsoft.com/services/active-directory-ds) を使用して作成できます。 各方法の詳細については、[ID ソリューションの比較](../../active-directory-domain-services/compare-identity-solutions.md)に関するページを参照してください。
- セキュリティ侵害を防ぐには、異なる組織のユーザーを別個の Azure AD テナントに保持する必要があります。 顧客組織ごとに 1 つの Active Directory ドメインと Azure Active Directory テナントを作成することをお勧めします。 そのテナントには、その顧客専用の Azure AD DS または AD DS サブスクリプションが関連付けられている必要があります。

次の 2 つのセクションでは、AD DS と Azure AD DS を使用して ID を作成する方法について説明します。 [組織間アプリのセキュリティ ガイドライン](security.md)に従うには、顧客ごとにこのプロセスを繰り返す必要があります。

## <a name="managing-users-with-active-directory-domain-services"></a>Active Directory Domain Services を使用したユーザーの管理

この方法では、ユーザー ID の管理と Azure AD への同期を行うために、Active Directory ドメイン コントローラーを使用してハイブリッド ID を設定します。

この方法には、Active Directory ドメイン コントローラーを設定してユーザー ID を管理する操作と、ユーザーを Azure AD に同期してハイブリッド ID を作成する操作が含まれます。 これらの ID は、Azure Virtual Desktop でホストされているアプリケーションにアクセスするために使用できます。 この構成では、ユーザーは Active Directory から Azure AD に同期され、セッション ホスト VM は AD DS ドメインに参加します。

AD DS で ID を設定するには:

1. [Azure AD テナント](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)と、顧客のサブスクリプションを作成します。

2. 顧客用に使用している Windows Server 仮想マシン (VM) に [Active Directory Domain Services をインストール](/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-)します。

3. ユーザー アカウントを Active Directory から Azure Active Directory に同期するために、ドメイン参加済みの別の VM に [Azure AD Connect](../../active-directory/hybrid/how-to-connect-install-roadmap.md) をインストールして構成します。

4. Intune を使用して VM を管理する予定の場合は、Azure AD Connect で [Hybrid Azure AD 参加済みデバイス](../../active-directory/devices/hybrid-azuread-join-plan.md)を有効にします。

5. 環境を構成したら、Active Directory に[新しいユーザーを作成](/previous-versions/windows/it-pro/windows-server-2003/cc755607(v=ws.10))します。 これらのユーザーは、Azure AD と自動的に同期されます。

6. セッション ホストをホスト プールにデプロイする場合は、Active Directory ドメイン名を使用して VM を参加させて、セッション ホストがドメイン コントローラーを確実に見通すことができるようにします。

この構成では環境をより細かく制御できますが、その複雑さによって管理の容易性が低下する可能性があります。 ただし、このオプションを使用すると、ユーザーに Azure AD ベースのアプリを提供できます。 また、Intune を使用してユーザーの VM を管理することもできます。

## <a name="managing-users-with-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を使用したユーザーの管理

Azure AD DS ID は、Microsoft が管理する Active Directory サービスとしてのプラットフォーム (PaaS) に格納されています。Microsoft によって管理される 2 つの AD ドメイン コントローラーにより、ユーザーは自分の Azure サブスクリプション内で AD DS を使用できます。 この構成では、ユーザーは Azure AD から Azure AD DS に同期され、セッション ホスト VM は Azure AD DS ドメインに参加します。 Azure AD DS ID は管理が簡単ですが、通常の AD DS ID ほど多くの制御を提供しません。 Azure Virtual Desktop VM は、Azure AD DS ドメインにのみ参加でき、Intune を使用して管理することはできません。

Azure AD DS を使用して ID を作成するには:

1. [Azure AD テナント](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)と、顧客のサブスクリプションを作成します。

2. ユーザーのサブスクリプションに [Azure AD Directory Services をデプロイ](../../active-directory-domain-services/tutorial-create-instance.md)します。

3. 環境の構成が完了したら、Azure Active Directory に[新しいユーザーを作成](../../active-directory/fundamentals/add-users-azure-active-directory.md)します。 これらのユーザー オブジェクトは、Azure AD DS と自動的に同期されます。

4. セッション ホストをホスト プールにデプロイする場合は、Azure AD DS ドメイン名を使用して VM を参加させます。

## <a name="next-steps"></a>次のステップ

ID とテナントを設定する際のセキュリティに関する考慮事項の詳細については、[組織間アプリのセキュリティ ガイドライン](security.md)に関するページを参照してください。

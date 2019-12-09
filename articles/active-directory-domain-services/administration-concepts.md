---
title: Azure AD Domain Services 管理の概念 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインとユーザー アカウントとパスワードの動作を管理する方法について説明します
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481021"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services でのユーザー アカウント、パスワード、および管理の管理の概念

Azure Active Directory Domain Services (AD DS) のマネージド ドメインを作成して実行すると、従来のオンプレミス AD DS 環境とは動作に違いがあります。 Azure AD DS でも自己管理型のドメインと同じ管理ツールを使用しますが、ドメイン コントローラー (DC) に直接アクセスすることはできません。 また、ユーザー アカウントの作成元よって、パスワード ポリシーとパスワード ハッシュの動作に違いがあります。

この概念に関する記事では、Azure AD DS マネージド ドメインの管理方法と、ユーザー アカウントの作成方法に応じたさまざまな動作について説明します。

## <a name="domain-management"></a>ドメインの管理

Azure AD DS では、ユーザー、グループ、資格情報、ポリシーなどのすべてのリソースが含まれているドメイン コントローラー (DC) は、マネージド サービスの一部です。 冗長性を確保するために、Azure AD DS マネージド ドメインの一部として 2 つの DC が作成されます。 これらの DC にサインインして管理タスクを実行することはできません。 代わりに、Azure AD DS マネージド ドメインに参加している管理 VM を作成してから、通常の AD DS 管理ツールをインストールします。 たとえば、DNS やグループ ポリシー オブジェクトなどの Active Directory 管理センターまたは Microsoft 管理コンソール (MMC) スナップインを使用できます。

## <a name="user-account-creation"></a>ユーザー アカウントの作成

ユーザー アカウントは、複数の方法で Azure AD DS に作成できます。 ほとんどのユーザー アカウントは Azure AD から同期されます。これには、オンプレミスの AD DS 環境から同期されたユーザー アカウントも含まれます。 Azure AD DS に直接アカウントを手動で作成することもできます。 初期パスワードの同期やパスワード ポリシーなどの一部の機能は、ユーザー アカウントの作成方法と作成場所に応じて異なる動作をします。

* ユーザー アカウントは Azure AD から同期できます。 これには、Azure AD で直接作成されたクラウド専用のユーザー アカウントと、Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたハイブリッド ユーザー アカウントが含まれます。
    * ほぼすべての Azure AD DS のユーザー アカウントは、Azure AD からの同期プロセスを通じて作成されています。
* ユーザー アカウントは Azure AD DS マネージド ドメインに手動で作成することができ、Azure AD には存在しません。
    * Azure AD DS でのみ実行されるアプリケーションのサービス アカウントを作成する必要がある場合は、マネージド ドメインに手動で作成することができます。 同期は Azure AD から一方向で行われるため、Azure AD DS で作成されたユーザー アカウントは Azure AD には同期されません。

## <a name="password-policy"></a>パスワード ポリシー

Azure AD DS には、アカウントのロックアウト、パスワードの最大有効期間、パスワードの複雑さなどの設定を定義する既定のパスワード ポリシーが含まれています。 アカウント ロックアウト ポリシーなどの設定は、前のセクションで説明したように、ユーザーの作成方法に関係なく、Azure AD DS 内のすべてのユーザーに適用されます。 パスワードの最小長やパスワードの複雑さなどの一部の設定は Azure AD DS で直接作成されたユーザーにのみ適用されます。

独自のカスタム パスワード ポリシーを作成して、Azure AD DS の既定のポリシーを上書きすることができます。 これらのカスタム ポリシーは、必要に応じて特定のユーザー グループに適用できます。

ユーザーの作成元で異なるパスワード ポリシーの適用方法の詳細については、「[マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー][password-policy]」を参照してください。

## <a name="password-hashes"></a>パスワード ハッシュ

Azure AD DS でマネージド ドメインのユーザーを認証するためには、NT LAN Manager (NTLM) 認証および Kerberos 認証に適した形式のパスワード ハッシュが必要となります。 NTLM 認証と Kerberos 認証に必要な形式のパスワード ハッシュは、ご利用のテナントに対して Azure AD DS を有効にするまで、Azure AD で生成または保存されることはありません。 また、セキュリティ上の理由から、クリアテキスト形式のパスワード資格情報が Azure AD に保存されることもありません。 そのため、こうした NTLM または Kerberos のパスワード ハッシュをユーザーの既存の資格情報に基づいて Azure AD が自動的に生成することはできません。

クラウド専用ユーザー アカウントの場合、ユーザーは Azure AD DS を使用する前に各自のパスワードを変更する必要があります。 このパスワード変更プロセスによって、Kerberos 認証と NTLM 認証に使用されるパスワード ハッシュが Azure AD に生成されて保存されます。

Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたユーザーについては、[パスワード ハッシュの同期を有効にします][hybrid-phs]。

> [!IMPORTANT]
> Azure AD Connect によってレガシ パスワード ハッシュが同期されるのは、Azure AD DS を Azure AD テナントに対して有効にしたときだけです。 Azure AD Connect を使用してオンプレミス AD DS 環境と Azure AD の同期しか行わない場合、レガシ パスワード ハッシュは使用されません。
>
> レガシ アプリケーションが NTLM 認証または LDAP simple bind を使用していない場合は、Azure AD DS の NTLM パスワード ハッシュ同期を無効にすることをお勧めします。 詳しくは、「[弱い暗号スイートと NTLM 資格情報ハッシュの同期を無効にする][secure-domain]」をご覧ください。

適切に構成されれば、使用可能なパスワード ハッシュが Azure AD DS のマネージド ドメインに保存されます。 Azure AD DS のマネージド ドメインを削除した場合、その時点で保存されていたパスワード ハッシュがあればすべて削除されます。 Azure AD DS のマネージド ドメインを後から作成した場合、Azure AD にある同期済みの資格情報は再利用できません。パスワード ハッシュを再度保存するには、パスワード ハッシュ同期を再構成する必要があります。 既にドメイン参加済みの VM またはユーザーがすぐに認証を行うことはできません。Azure AD が、新しい Azure AD DS のマネージド ドメインにパスワード ハッシュを生成して保存する必要があります。 詳細については、[Azure AD DS と Azure AD Connect のパスワード ハッシュ同期プロセス][azure-ad-password-sync]に関するセクションを参照してください。

> [!IMPORTANT]
> Azure AD Connect は、オンプレミスの AD DS 環境との同期のためにのみインストールおよび構成する必要があります。 オブジェクトを Azure AD に同期するために、Azure AD DS マネージド ドメインに Azure AD Connect をインストールすることはサポートされていません。

## <a name="forests-and-trusts"></a>フォレストと信頼

"*フォレスト*" は、Active Directory Domain Services (AD DS) で 1 つまたは複数の "*ドメイン*" をグループ化するために使われる論理構造です。 ドメインには、ユーザーまたはグループのオブジェクトが格納され、認証サービスが提供されます。

Azure AD DS では、フォレストにはドメインが 1 つだけ含まれます。 多くの場合、オンプレミスの AD DS フォレストには多数のドメインが含まれます。 大規模な組織では、特に合併や買収の後に、複数のオンプレミス フォレストが存在し、各フォレストにそれぞれ複数のドメインが含まれている場合があります。

既定では、Azure AD DS マネージド ドメインは "*ユーザー*" フォレストとして作成されます。 このタイプのフォレストでは、オンプレミスの AD DS 環境で作成されたユーザー アカウントも含め、Azure AD 内のすべてのオブジェクトが同期されます。 ドメインに参加している VM へのサインインなどのために、Azure AD DS マネージド ドメインに対してユーザー アカウントを直接認証できます。 ユーザー フォレストが機能するのは、パスワード ハッシュの同期が可能で、ユーザーがスマート カード認証などの専用サインイン方法を使用していない場合です。

Azure AD DS の "*リソース*" フォレストでは、ユーザーは自身のオンプレミス AD DS からの、一方向のフォレストの "*信頼*" を介して認証されます。 この方法では、ユーザー オブジェクトとパスワード ハッシュが Azure AD DS に同期されません。 ユーザー オブジェクトと資格情報は、オンプレミスの AD DS にのみ存在します。 このアプローチを使用すると、企業は、LDAPS、Kerberos、NTLM などの従来の認証に依存するリソースとアプリケーション プラットフォームを Azure でホストでき、一方で認証に関する問題や懸念事項が取り除かれます。 Azure AD DS リソース フォレストは現在、プレビューの段階です。

Azure AD DS 内のフォレストの種類の詳細については、[リソース フォレストとは何か][concepts-forest]および[フォレストの信頼が Azure AD DS で機能する方法][concepts-trust]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

まず初めに、[Azure AD DS マネージド ドメインを作成しましょう][create-instance]。

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

---
title: 概要 - Azure Files の ID ベースの承認
description: Azure Files は、Azure Active Directory Domain Services (AD DS) および Active Directory を使用した、SMB (Server Message Block) 上の ID ベースの認証をサポートします。 ドメインに参加している Windows 仮想マシン (VM) は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536584"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>SMB アクセスに対する Azure Files ID ベース認証サポートの概要
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure ファイル共有 (プレビュー) に対してオンプレミス Active Directory Domain Services 認証を有効にする方法については、「[SMB を使用して Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-enable.md)」を参照してください。

Azure ファイル共有に対して Azure AD DS 認証を有効にする方法については、「[Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-domain-service-enable.md)」をご覧ください。

## <a name="glossary"></a>用語集 
SMB を使用した Azure ファイル共有の Azure AD Domain Service 認証に関連するいくつかの重要な用語を理解することをお勧めします。

-   **Kerberos 認証**

    Kerberos は、ユーザーまたはホストの身元を確認するために使用される認証プロトコルです。 Kerberos の詳細については、「[Kerberos 認証の概要](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)」を参照してください。

-  **サーバー メッセージ ブロック (SMB) プロトコル**

    SMB は、業界標準のネットワーク ファイル共有プロトコルです。 SMB は、Common Internet File System (CIFS) とも呼ばれます。 SMB の詳細については、「[Microsoft SMB プロトコルと CIFS プロトコルの概要](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)」を参照してください。

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) は、Microsoft のマルチテナント クラウド ベースのディレクトリおよび ID 管理サービスです。 Azure AD には、主要なディレクトリ サービス、アプリケーション アクセスの管理、ID 保護の機能が一つのソリューションとして統合されています。 Azure AD に参加している Windows 仮想マシン (VM) は、Azure AD 資格情報を使用して Azure ファイル共有にアクセスできます。 詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください。

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS は、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証など) を提供します。 これらのサービスは、Active Directory Domain Services と完全に互換性があります。 詳細については、[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) に関する記事を参照してください。

- **オンプレミス Active Directory Domain Services (AD DS)**

    オンプレミス Active Directory Domain Services (AD DS) の Azure Files との統合 (プレビュー) により、ディレクトリ データをネットワークのユーザーと管理者が使用できるようにしながら、それを格納するための方法が提供されます。 AD DS のセキュリティは、ログオン認証とディレクトリ内のオブジェクトに対するアクセス制御によって実現されています。 管理者は、シングル ネットワーク ログオンで、ネットワーク全体のディレクトリ データおよび組織を管理できます。また、承認されたネットワーク ユーザーは、ネットワーク上の任意の場所にあるリソースにアクセスできます。 AD DS は、通常、オンプレミス環境の企業によって採用され、アクセス制御の ID として AD DS 資格情報が使用されます。 詳細については、「[Active Directory Domain Services の概要](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。

-   **Azure のロールベースのアクセス制御 (RBAC)**

    Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用して業務遂行に必要な最小限の権限をユーザーに付与することで、リソースへのアクセスを管理できます。 RBAC の詳細については、「[Azure のロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

## <a name="common-use-cases"></a>一般的なユース ケース

ID ベースの認証と Azure Files での Windows ACL のサポートは、次のユースケースに最適です。

### <a name="replace-on-premises-file-servers"></a>オンプレミスのファイル サーバーの置換

散在するオンプレミスのファイル サーバーの非推奨と置換は、すべての企業が IT の近代化において直面する一般的な問題です。 データを Azure Files に移行できる場合は、オンプレミス AD DS (プレビュー) 認証を使用した Azure ファイル共有が最適です。 移行が完了すると、高可用性とスケーラビリティの利点を活用できるだけでなく、クライアント側の変更を最小限に抑えることができます。 既存のドメイン参加済みのマシンを使用して、同じ資格情報で引き続きデータにアクセスできるよう、エンド ユーザーにシームレスな移行エクスペリエンスが提供されます。

### <a name="lift-and-shift-applications-to-azure"></a>Azure へのアプリケーションのリフト アンド シフト

アプリケーションをクラウドにリフト アンド シフトする場合は、データに対して同じ認証モデルを維持する必要があります。 ID ベースのアクセス制御エクスペリエンスを Azure ファイル共有に拡張すると、アプリケーションを最新の認証方法に変更し、迅速にクラウドを導入する必要がなくなります。 Azure ファイル共有には、認証のために Azure AD DS またはオンプレミス AD DS (プレビュー) のいずれかと統合するオプションが用意されています。 100% クラウド ネイティブを目指し、クラウド インフラストラクチャの管理作業を最小化することを計画している場合、Azure AD DS はフル マネージドのドメイン サービスとして、より適しています。 AD DS 機能との完全な互換性が必要な場合は、VM 上の自己ホスト型ドメイン コントローラーによって AD DS 環境をクラウドに拡張することを検討してください。 どちらの方法でも、ビジネス ニーズに合ったドメイン サービスを柔軟に選択できます。

### <a name="backup-and-disaster-recovery-dr"></a>バックアップとディザスター リカバリー (DR)

プライマリ ファイル ストレージをオンプレミスで保持している場合、Azure ファイル共有はバックアップや DR のための理想的なストレージとして機能し、ビジネス継続性を向上させることができます。 Azure ファイル共有を使用すると、Windows DACL を維持したまま、既存のファイル サーバーからデータをバックアップできます。 DR シナリオでは、フェールオーバー時の適切なアクセス制御の適用をサポートする認証オプションを構成できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

次の表は、Azure AD DS とオンプレミス AD DS (プレビュー) でサポートされている Azure ファイル共有の認証シナリオをまとめたものです。 Azure Files との統合のために、クライアント環境に採用したドメイン サービスを選択することをお勧めします。 AD DS (プレビュー) が、デバイスが AD にドメイン参加済みのオンプレミスまたは Azure に既にセットアップされている場合は、Azure ファイル共有認証に AD DS (プレビュー) を利用する必要があります。 同様に、Azure AD DS (GA) を既に採用している場合は、それを Azure ファイル共有の認証に使用する必要があります。


|Azure AD DS 認証  | オンプレミス AD DS (プレビュー) 認証  |
|---------|---------|
|Azure AD DS に参加済みの Windows マシンは、SMB 経由で Azure AD 資格情報を使用して Azure ファイル共有にアクセスできます。     |オンプレミス AD DS に参加済みの Windows マシンは、SMB 経由で Azure AD に同期されるオンプレミス Active Directory 資格情報を使用して Azure ファイル共有にアクセスできます。         |

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

- Azure AD DS とオンプレミス AD DS の認証では、コンピューター アカウントに対する認証はサポートされていません。 代わりに、サービス ログオン アカウントを使用することを検討してください。
- Azure AD DS 認証では、Azure AD に参加済みのデバイスに対する認証はサポートされていません。

## <a name="advantages-of-identity-based-authentication"></a>ID ベースの認証の利点
Azure Files に対する ID ベースの認証には、共有キー認証と比較して、以下のようなさまざまな利点があります。

-   **オンプレミス AD DS と Azure AD DS を使用して、従来の ID ベースのファイル共有アクセス エクスペリエンスをクラウドに拡張する**  
    従来のファイル サーバーを Azure ファイル共有に置き換えて、アプリケーションをクラウドにリフト アンド シフトする場合は、アプリケーションによるファイル データへのアクセスをオンプレミス AD DS または Azure AD DS 資格情報を使用して認証することができます。 Azure Files では、オンプレミス AD DS または Azure AD DS 資格情報の両方を使用して、オンプレミス AD DS または Azure AD DS ドメイン参加済み VM のいずれかから SMB 経由で Azure ファイル共有にアクセスできます。

-   **Azure のファイル共有への詳細なアクセス制御の適用が可能**  
    特定の ID に対して、ファイル共有、ディレクトリ、またはファイル レベルでアクセス許可を付与できます。 たとえば、プロジェクトのコラボレーションのために、複数のチームが 1 つの Azure ファイル共有を使用しているとします。 その場合、機密ではないディレクトリについてはすべてのチームにアクセス権限を付与し、機密の財務データが含まれるディレクトリについては財務チームのみにアクセス権限を付与できます。 

-   **データと共に Windows ACL (NTFS とも呼ばれます) をバックアップする**  
    Azure ファイル共有では、既存のオンプレミスのファイル共有をバックアップすることができます。 Azure Files では、SMB 経由で Azure ファイル共有に共有ファイルをバックアップすると、データと共に ACL も保持されます。

## <a name="how-it-works"></a>しくみ

Azure ファイル共有では、Azure AD DS またはオンプレミス AD DS (プレビュー) と統合するための Kerberos 認証がサポートされています。 Azure ファイル共有で認証を有効にする前に、まずドメイン環境を設定する必要があります。 Azure AD DS 認証の場合、Azure AD Domain Services を有効にし、ファイル データのアクセス元となる VM をドメイン参加させる必要があります。 ドメイン参加している VM は、Azure AD DS と同じ仮想ネットワーク (VNET) に存在する必要があります。 同様に、オンプレミス AD DS (プレビュー) 認証の場合は、ドメイン コントローラーを設定し、マシンまたは VM をドメイン参加させる必要があります。

VM 上で実行されているアプリケーションに関連付けられている ID が Azure ファイル共有のデータにアクセスしようとした場合、その要求は Azure AD DS に送信され、ID が認証されます。 認証が成功した場合、Azure AD DS は Kerberos トークンを返します。 アプリケーションは Kerberos トークンが含まれる要求を送信し、Azure ファイル共有はそのトークンを使用して要求を承認します。 Azure ファイル共有はトークンのみを受け取り、Azure AD DS 資格情報は保持しません。 オンプレミス AD DS 認証は、AD DS が Kerberos トークンを提供する場合と同様の方法で動作します。

![SMB 経由の Azure AD 認証のスクリーン ショット](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>ID ベースの認証を有効にする

新規および既存のストレージ アカウントで、Azure ファイル共有の Azure AD DS またはオンプレミス AD DS (プレビュー) のいずれかを使用して ID ベースの認証を有効にできます。 ストレージ アカウントでのファイル アクセス認証に使用できるドメイン サービスは 1 つだけです。これは、アカウント内のすべてのファイル共有に適用されます。 Azure AD DS での認証用のファイル共有の設定に関する詳細なガイダンスについては、「[Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-domain-service-enable.md)」という記事を、オンプレミス AD DS (プレビュー) のガイダンスについては、「[SMB を使用して Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-enable.md)」という他の記事を参照してください。

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files の共有レベルのアクセス権限を構成する

Azure AD DS またはオンプレミス AD DS (プレビュー) 認証のいずれかを有効にすると、組み込みの RBAC ロールを使用したり、Azure AD ID のカスタム ロールを構成したり、ストレージ アカウント内の任意のファイル共有にアクセス権を割り当てたりすることができます。 割り当てられたアクセス許可により、付与された ID は共有のみにアクセスできます。それ以外には、ルート ディレクトリであってもアクセスできません。 その場合でも、Azure ファイル共有に対してディレクトリまたはファイル レベルのアクセス許可を個別に構成する必要があります。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Azure Files のディレクトリ レベルまたはファイル レベルの権限を構成する

Azure ファイル共有では、ルート ディレクトリを含む、ディレクトリとファイルの両方のレベルで標準の Windows ファイルのアクセス許可が適用されます。 ディレクトリ レベルまたはファイル レベルの権限の構成は、SMB 経由および REST 経由での構成がサポートされています。 VM 上で対象のファイル共有をマウントし、Windows のエクスプローラー、Windows の [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) または [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) コマンドを使用して権限を構成します。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>スーパーユーザーの権限でストレージ アカウントのキーを使用する

ストレージ アカウント キーを保持するユーザーは、スーパー ユーザーの権限で Azure ファイル共有にアクセスできます。 スーパーユーザーのアクセス許可は、すべてのアクセス制御制限をバイパスします。

> [!IMPORTANT]
> 推奨されるセキュリティのベスト プラクティスは、ストレージ アカウント キーを共有しないこと、および可能な限り ID ベースの認証を利用することです。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Azure ファイル共有にデータをインポートするときに、ディレクトリとファイル ACL を保持する

Azure Files では、Azure ファイル共有にデータをコピーする際、ディレクトリまたはファイル レベルの ACL の保持がサポートされています。 Azure File Sync または一般的なファイル移動ツールセットを使用して、ディレクトリまたはファイルの ACL を Azure ファイル共有にコピーできます。 たとえば、[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) を `/copy:s` フラグと共に使用して、Azure ファイル共有にデータや ACL をコピーすることができます。 ACL は既定で保持されます。ACL を維持するために、ストレージ アカウントで ID ベースの認証を有効にする必要はありません。

## <a name="pricing"></a>価格
お使いのストレージ アカウントで SMB 経由の ID ベースの認証を有効にする場合、追加のサービス料金は発生しません。 価格の詳細については、「[Azure Files の価格](https://azure.microsoft.com/pricing/details/storage/files/)」と「[Azure AD Domain Services の価格](https://azure.microsoft.com/pricing/details/active-directory-ds/)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure Files や、SMB 経由の ID ベースの認証の詳細については、これらのリソースを参照してください。

- [Azure Files のデプロイの計画](storage-files-planning.md)
- [SMB を使用して Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-enable.md)
- [Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)

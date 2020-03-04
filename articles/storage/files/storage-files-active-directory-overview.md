---
title: 概要 - Azure Files の ID ベースの承認
description: Azure Files は、Azure Active Directory Domain Services (AD DS) および Active Directory を使用した、SMB (Server Message Block) 上の ID ベースの認証をサポートします。 ドメインに参加している Windows 仮想マシン (VM) は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 673bf3be59d72b2cc62b9f92af353fee235c5ddc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598818"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>SMB アクセスに対する Azure Files ID ベース認証サポートの概要
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure ファイル共有の AD 認証を有効にする方法については、「[Azure ファイル共有に対する SMB 経由の Azure Active Directory 認証を有効にする](storage-files-identity-auth-active-directory-enable.md)」を参照してください。

Azure ファイル共有に対して Azure AD DS 認証を有効にする方法については、「[Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-domain-service-enable.md)」をご覧ください。

## <a name="glossary"></a>用語集 
SMB を使用した Azure ファイル共有の Azure AD Domain Service 認証に関連するいくつかの重要な用語を理解することをお勧めします。

-   **Kerberos 認証**

    Kerberos は、ユーザーまたはホストの身元を確認するために使用される認証プロトコルです。 Kerberos の詳細については、「[Kerberos 認証の概要](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)」を参照してください。

-  **サーバー メッセージ ブロック (SMB) プロトコル**

    SMB は、業界標準のネットワーク ファイル共有プロトコルです。 SMB は、Common Internet File System (CIFS) とも呼ばれます。 SMB の詳細については、「[Microsoft SMB プロトコルと CIFS プロトコルの概要](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)」を参照してください。

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。 Azure AD には、主要なディレクトリ サービス、アプリケーション アクセスの管理、ID 保護の機能が一つのソリューションとして統合されています。 Azure AD を使用すると、ドメインに参加している Windows 仮想マシン (VM) が Azure AD の資格情報で Azure ファイル共有にアクセスできるようになります。 詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください。

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) では、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証) を提供します。 これらのサービスは、Windows Server Active Directory と完全に互換性があります。 詳細については、「 [Azure Active Directory (AD) のドメイン サービス](../../active-directory-domain-services/overview.md)」を参照してください。

- **Active Directory Domain Services (AD DS、AD とも呼ばれます)**

    Active directory (AD) (プレビュー) は、ネットワーク ユーザーと管理者がディレクトリ データを使用できるようにしつつ、格納するためのメソッドを提供します。 Active Directory のセキュリティは、ログオン認証およびディレクトリ内のオブジェクトに対するアクセス制御によって実現されています。 管理者は、シングル ネットワーク ログオンで、ネットワーク全体のディレクトリ データおよび組織を管理できます。また、承認されたネットワーク ユーザーは、ネットワーク上の任意の場所にあるリソースにアクセスできます。 AD は、通常、オンプレミスの企業によって採用され、アクセス制御の ID として AD 資格情報が使用されます。 詳細については、「[Active Directory Domain Services の概要](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)」を参照してください。

-   **Azure のロールベースのアクセス制御 (RBAC)**

    Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用して業務遂行に必要な最小限の権限をユーザーに付与することで、リソースへのアクセスを管理できます。 RBAC の詳細については、「[Azure のロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

## <a name="common-use-cases"></a>一般的なユース ケース

ID ベースの認証と Azure Files での Windows ACL のサポートは、次のユースケースに最適です。

### <a name="replace-on-premises-file-servers"></a>オンプレミスのファイル サーバーの置換

散在するオンプレミスのファイル サーバーの非推奨と置換は、すべての企業が IT の近代化において直面する一般的な問題です。 データを Azure Files に移行できる場合は、AD (プレビュー) 認証を使用した Azure ファイル共有が最適です。 移行が完了すると、高可用性とスケーラビリティの利点を活用できるだけでなく、クライアント側の変化 (特に複雑な AD ドメイン インフラストラクチャ) を最小限に抑えることができます。 既存のドメイン参加済みのマシンを使用して、同じ資格情報で引き続きデータにアクセスできるよう、エンド ユーザーにシームレスな移行エクスペリエンスが提供されます。

### <a name="lift-and-shift-applications-to-azure"></a>Azure へのアプリケーションのリフト アンド シフト

アプリケーションを "リフト アンド シフト" でクラウドに移行する場合は、データに対して同じ認証モデルを維持する必要があります。 ID ベースのアクセス制御エクスペリエンスを Azure ファイル共有に拡張すると、アプリケーションを最新の認証方法に変更し、迅速にクラウドを導入する必要がなくなります。 Azure ファイル共有には、認証用に Azure AD DS (GA) または AD (プレビュー) のいずれかと統合するオプションが用意されています。 100% クラウド ネイティブを目指し、クラウド インフラストラクチャの管理作業を最小化することを計画している場合、Azure AD DS はフル マネージドのドメイン サービスとして、より適しています。 AD DS (GA) 機能との完全な互換性が必要な場合は、VM 上の自己ホスト型のドメイン コントローラーを使用して AD 環境をクラウドに拡張することを検討してください。 どちらの方法でも、ビジネス ニーズに合ったドメイン サービスを柔軟に選択できます。

### <a name="backup-and-disaster-recovery-dr"></a>バックアップとディザスター リカバリー (DR)

プライマリ ファイル ストレージをオンプレミスで保持している場合、Azure ファイル共有はバックアップや DR のための理想的なストレージとして機能し、ビジネス継続性を向上させることができます。 Azure ファイル共有を使用すると、Windows DACL を維持したまま、既存のファイル サーバーからデータをバックアップできます。 DR シナリオでは、フェールオーバー時の適切なアクセス制御の適用をサポートする認証オプションを構成できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

次の表は、Azure AD DS (GA) と AD (プレビュー) でサポートされている Azure ファイル共有の認証シナリオをまとめたものです。 Azure Files との統合のために、クライアント環境に採用したドメイン サービスを選択することをお勧めします。 AD (プレビュー) が、デバイスが AD にドメイン参加済みのオンプレミスまたは Azure で既にセットアップされている場合は、Azure ファイル共有認証に AD (プレビュー) を利用することが推薦されます。 同様に、Azure AD DS (GA) を既に採用している場合は、それを Azure ファイル共有の認証に使用する必要があります。


|Azure AD DS (GA) 認証  |AD (プレビュー) 認証  |
|---------|---------|
|Azure AD DS ドメイン参加済み Windows マシンは、SMB 経由の Azure AD 資格情報を使用して Azure ファイル共有にアクセスできます。     |AD ドメイン参加済み Windows マシンは、SMB 経由で Azure AD に同期される AD 資格情報を使用して Azure ファイル共有にアクセスできます。         |

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

- Azure AD DS (GA) および AD (プレビュー) 認証では、コンピューター アカウントに対する認証はサポートされていません。 代わりに、サービス ログオン アカウントを使用することを検討してください。
- Azure AD DS (GA) 認証では、Azure AD クラウド参加済みデバイスに対する認証はサポートされていません。

## <a name="advantages-of-identity-based-authentication"></a>ID ベースの認証の利点
Azure Files に対する ID ベースの認証には、共有キー認証と比較して、以下のようなさまざまな利点があります。

-   **従来の ID ベースのファイル共有アクセス エクスペリエンスを AD と Azure AD DS を使用してクラウド上にデプロイする**  
    従来のファイル サーバーを Azure ファイル共有に置き換えて、お使いのアプリケーションを "リフト アンド シフト" でクラウドに移行する場合は、アプリケーションによるファイル データへのアクセスを AD または Azure AD 資格情報で認証することができます。 Azure Files では、AD または Azure AD 資格情報の両方を使用して、AD または Azure AD DS ドメイン参加済み VM のいずれかから SMB 経由で Azure ファイル共有にアクセスできます。

-   **Azure のファイル共有への詳細なアクセス制御の適用が可能**  
    特定の ID に対して、ファイル共有、ディレクトリ、またはファイル レベルでアクセス許可を付与できます。 たとえば、プロジェクトのコラボレーションのために、複数のチームが 1 つの Azure ファイル共有を使用しているとします。 その場合、機密ではないディレクトリについてはすべてのチームにアクセス権限を付与し、機密の財務データが含まれるディレクトリについては財務チームのみにアクセス権限を付与できます。 

-   **データと共に Windows ACL (NTFS とも呼ばれます) をバックアップする**  
    Azure ファイル共有では、既存のオンプレミスのファイル共有をバックアップすることができます。 Azure Files では、SMB 経由で Azure ファイル共有に共有ファイルをバックアップすると、データと共に ACL も保持されます。

## <a name="how-it-works"></a>しくみ
Azure ファイル共有では、Azure AD DS (GA) または AD (プレビュー) と統合するための kerberos 認証がサポートされています。 Azure ファイル共有で認証を有効にする前に、まずドメイン環境をセットアップする必要があります。 Azure AD DS (GA) 認証の場合、Azure AD Domain Services を有効にし、ファイル データのアクセス元となる VM をドメイン参加する必要があります。 ドメイン参加している VM は、Azure AD Domain Services と同じ仮想ネットワーク (VNET) 内に存在する必要があります。 同様に、AD (プレビュー) 認証の場合は、Active Directory ドメイン コントローラーを設定し、マシンまたは VM にドメイン参加させる必要があります。

VM 上で実行されているアプリケーションに関連付けられている ID が Azure ファイル共有のデータにアクセスしようとした場合、その要求は Azure AD Domain Services に送信され、ID が認証されます。 認証が成功した場合、Azure AD Domain Services は Kerberos トークンを返します。 アプリケーションは Kerberos トークンが含まれる要求を送信し、Azure ファイル共有はそのトークンを使用して要求を承認します。 Azure ファイル共有はトークンのみを受け取り、Azure AD の資格情報は保持しません。 AD 認証は、AD が Kerberos トークンを提供するのと同様の方法で動作します。

![SMB 経由の Azure AD 認証のスクリーン ショット](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>ID ベースの認証を有効にする

新規および既存のストレージ アカウントで、Azure ファイル共有の Azure AD DS (GA) または AD (プレビュー) のいずれかを使用して ID ベースの認証を有効にすることができます。 ストレージ アカウントでのファイル アクセス認証に使用できるドメイン サービスは 1 つだけです。これは、アカウント内のすべてのファイル共有に適用されます。 Azure AD DS (GA) での認証用のファイル共有の設定に関する詳細な手順については[「Azure Files に対する Azure Active Directory Domain Services 認証を有効にする」](storage-files-identity-auth-active-directory-domain-service-enable.md)を、AD (プレビュー) に関するガイドについては、「[Azure ファイル共有に対する SMB 経由の Azure Active Directory 認証を有効にする](storage-files-identity-auth-active-directory-enable.md)」を参照してください。

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files の共有レベルのアクセス権限を構成する

Azure AD DS (GA) または AD (プレビュー) 認証のいずれかを有効にすると、組み込みの RBAC ロールを使用したり、Azure AD ID のカスタム ロールを構成したり、ストレージ アカウント内の任意のファイル共有にアクセス権を割り当てたりすることができます。 アクセス許可を割り当てることで、付与された ID は共有のみにアクセスでき、それ以外はルート ディレクトリであってもアクセスできません。 その場合でも、Azure ファイル共有に対してディレクトリまたはファイル レベルのアクセス許可を個別に構成する必要があります。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Azure Files のディレクトリ レベルまたはファイル レベルの権限を構成する

Azure ファイル共有では、ルート ディレクトリを含む、ディレクトリ レベルとファイル レベルの両方で標準の Windows ファイルのアクセス許可が適用されます。 ディレクトリ レベルまたはファイル レベルの権限の構成は、SMB 経由および REST 経由での構成がサポートされています。 VM 上で対象のファイル共有をマウントし、Windows のエクスプローラー、Windows の [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) または [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) コマンドを使用して権限を構成します。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>スーパーユーザーの権限でストレージ アカウントのキーを使用する

ストレージ アカウント キーを保持するユーザーは、スーパー ユーザーの権限で Azure ファイル共有にアクセスできます。 スーパーユーザーのアクセス許可は、すべてのアクセス制御制限をバイパスします。

> [!IMPORTANT]
> 推奨されるセキュリティのベスト プラクティスは、ストレージ アカウント キーを共有しないこと、および可能な限り ID ベースの認証を利用することです。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Azure ファイル共有にデータをインポートするときに、ディレクトリとファイル ACL を保持する

Azure Files では、Azure ファイル共有にデータをコピーする際、ディレクトリまたはファイル レベルの ACL の保持がサポートされています。 Azure File Sync または一般的なファイル移動ツールセットを使用して、ディレクトリまたはファイルの ACL を Azure ファイル共有にコピーできます。 たとえば、[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) を `/copy:s` フラグと共に使用して、Azure ファイル共有にデータや ACL をコピーすることができます。 ACL は既定で保持されます。ACL を維持するために、ストレージ アカウントで ID ベースの認証を有効にする必要はありません。

## <a name="pricing"></a>価格
お使いのストレージ アカウントで SMB 経由の ID ベースの認証を有効にする場合、追加のサービス料金は発生しません。 価格の詳細については、AAD DS 情報をお探しの場合は、「[Azure Files の価格](https://azure.microsoft.com/pricing/details/storage/files/)」と「[Azure AD Domain Services の価格](https://azure.microsoft.com/pricing/details/active-directory-ds/)」のページを参照してください。

## <a name="next-steps"></a>次のステップ
Azure Files や、SMB 経由の ID ベースの認証の詳細については、これらのリソースを参照してください。

- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure ファイル共有に対する SMB 経由の Azure Active Directory 認証を有効にする](storage-files-identity-auth-active-directory-enable.md)
- [Azure Files に対する Azure Active Directory Domain Services 認証を有効にする](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)

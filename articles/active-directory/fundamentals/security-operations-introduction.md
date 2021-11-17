---
title: Azure Active Directory セキュリティ運用ガイド
description: アカウント、アプリケーション、デバイス、およびインフラストラクチャに関するセキュリティの問題を監視、特定、ならびに警告する方法について説明します
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e9921204a60f4364b2365139e2a8b07b4b8e7f0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300826"
---
# <a name="azure-active-directory-security-operations-guide"></a>Azure Active Directory セキュリティ運用ガイド

Microsoft では、コントロール プレーンとして ID を利用した[多層防御](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth)原則により、実績のある[ゼロ トラスト セキュリティ](https://aka.ms/Zero-Trust)へのアプローチを成功させてきました。 組織がスケール、コスト削減、およびセキュリティを追求し、ハイブリッドのワークロードの世界を受け入れ続ける中、Azure Active Directory (Azure AD) は、ID 管理の戦略において非常に重要な役割を果たします。 最近では、ID とセキュリティの侵害に関するニュースにより、企業の IT 部門は、ID セキュリティ態勢を、防御的セキュリティ成功の指標として捉えるようになりました。

さらに組織はオンプレミスとクラウドのアプリケーションを組み合わせて使用する必要があり、ユーザーはそれらのアプリケーションに、オンプレミスとクラウド専用の両方のアカウントでアクセスします。 オンプレミスとクラウドの両方でユーザー、アプリケーション、デバイスを管理するのは困難なシナリオです。

Azure Active Directory では、場所に関係なく、すべてのリソースに対する認証と承認を行うための、共通のユーザー ID を作成します。 これをハイブリッド ID と呼んでいます。 

Azure AD でハイブリッド ID を実現するために、お使いのシナリオに応じて、3 つの認証方法のうち 1 つを使用できます。 次に 3 つの方法を示します。

* [パスワード ハッシュの同期 (PHS)](../hybrid/whatis-phs.md)

* [パススルー認証 (PTA)](../hybrid/how-to-connect-pta.md)

* [フェデレーション (AD FS)](../hybrid/whatis-fed.md)

現在のセキュリティ操作を監査したり、または Azure 環境のセキュリティ運用を確立したりする際には、次のことをお勧めします。

* Microsoft セキュリティ ガイダンスの特定の部分を読み、クラウドベースまたはハイブリッド Azure 環境のセキュリティ保護に関する知識のベースラインを確立します。

* アカウントとパスワードの戦略と認証方法を監査し、最も一般的な攻撃ベクトルを抑止します。

* セキュリティ上の脅威を示す可能性があるアクティビティについて、継続的な監視とアラートを行うための戦略を作成します。

## <a name="audience"></a>対象ユーザー

Azure AD SecOps ガイドは、より高度な ID セキュリティ構成と監視プロファイルを通じて脅威に対抗する必要がある、企業の IT ID セキュリティ運用チームおよび管理サービス プロバイダーを対象としています。 このガイドは、セキュリティ オペレーション センター (SOC) の防御と侵入テストのチームに対し、ID のセキュリティ態勢の改善および維持についてアドバイスする、IT 管理者や ID アーキテクトにとって特に有益です。 

## <a name="scope"></a>Scope

この概要では、事前にお読みいただきたい推奨文書やパスワードの監査と戦略に関する推奨事項について説明します。 またこの記事では、ハイブリッド Azure 環境および完全なクラウドベースの Azure 環境で使用できるツールの概要についても説明します。 最後に、監視、アラート、およびセキュリティ情報イベント管理 (SIEM) の戦略と環境を構成するために使用できるデータ ソースの一覧を提供します。 このガイドの残りの部分では、次の領域における監視およびアラートの戦略について説明します。

* [ユーザー アカウント](security-operations-user-accounts.md) – 管理者特権を持たない、非特権ユーザー アカウント特有のガイダンス。異常なアカウントの作成と使用、および異常なサインインなどが含まれます。

* [特権アカウント](security-operations-privileged-accounts.md) – 管理タスクを実行するための昇格されたアクセス許可を持つ特権ユーザー アカウント特有のガイダンス。これには、Azure AD ロールの割り当て、Azure リソース ロールの割り当て、Azure リソースとサブスクリプションのアクセス管理などが含まれます。

* [Privileged Identity Management (PIM)](security-operations-privileged-identity-management.md) – PIM を使用してリソースへのアクセスを管理、制御、監視する方法特有のガイダンス。 

* [アプリケーション](security-operations-applications.md) – アプリケーションの認証を提供するために使用されるアカウント特有のガイダンス。 

* [デバイス](security-operations-devices.md) – ポリシーの外部で登録または参加したデバイスの監視とアラート、非準拠の使用、デバイス管理ロールの管理、および仮想マシンへのサインイン特有のガイダンス。

*  [インフラストラクチャ](security-operations-infrastructure.md) – ハイブリッド環境および純粋なクラウドベースの環境に対する脅威の監視とアラート特有のガイダンス。

## <a name="important-reference-content"></a>重要なリファレンス コンテンツ

Microsoft には、お客様のニーズに合わせて IT 環境をカスタマイズできる製品とサービスが多数用意されています。 監視とアラートの戦略の一環として、お使いのオペレーティング環境に関連する次のガイダンスを確認することをお勧めします。

* Windows オペレーティング システム

   * [Windows 10 と Windows Server 2016 セキュリティ監査と監視のリファレンス](https://www.microsoft.com/download/details.aspx?id=52630)

   * [Windows 10 v1909 と Windows Server v1909 のセキュリティ ベースライン (最終)](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/security-baseline-final-for-windows-10-v1909-and-windows-server/ba-p/1023093)

   * [Windows 11 用のセキュリティ ベースライン](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-11-security-baseline/ba-p/2810772)
   
   * [Windows Server 2022 用のセキュリティ ベースライン](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/windows-server-2022-security-baseline/ba-p/2724685)
   
* オンプレミス環境

   * [Microsoft Defender for Identity のアーキテクチャ](/defender-for-identity/architecture)

   * [クイックスタート: Microsoft Defender for Identity を Active Directory に接続する](/defender-for-identity/install-step2)

   * [Microsoft Defender for Identity の Azure セキュリティ ベースライン](/defender-for-identity/security-baseline)

   * [Active Directory の侵害の兆候を監視する](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)

* クラウドベースの Azure 環境


   * [Azure AD のサインイン ログを使用してサインインを監視する](../reports-monitoring/concept-all-sign-ins.md)

   * [Azure Active Directory ポータルの監査アクティビティ レポート](../reports-monitoring/concept-audit-logs.md)

   * [Azure Active Directory Identity Protection を使用したリスクの調査](../identity-protection/howto-identity-protection-investigate-risk.md) 

   * [Microsoft Sentinel に Azure AD Identity Protection データを接続する](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)

* Active Directory Domain Services (AD DS)

   * [監査ポリシーの推奨事項](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)

* Active Directory フェデレーション サービス (AD FS)

   * [AD FS のトラブルシューティング - イベントとログの監査](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging)

## <a name="data-sources"></a>データ ソース 

調査と監視に使用するログ ファイルは次のとおりです。

* [Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)

* [サインイン ログ](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 監査ログ](/microsoft-365/compliance/auditing-solutions-overview)

* [Azure Key Vault のログ](../../key-vault/general/logging.md?tabs=Vault)

Azure portal から、Azure AD 監査ログを表示したり、コンマ区切り値 (CSV) または JavaScript Object Notation (JSON) ファイルとしてダウンロードしたりできます。 Azure portal には、Azure AD ログを他のツールと統合する方法がいくつか用意されており、監視とアラートの自動化を強化することができます。

* **[Microsoft Sentinel](../../sentinel/overview.md)** – セキュリティ情報イベント管理 (SIEM) 機能を備え、エンタープライズ レベルでインテリジェントにセキュリティを分析します。 

* **[Azure Monitor](../../azure-monitor/overview.md)** - さまざまな条件に基づいて監視とアラートを自動化します。 ブックを作成または使用して、異なるソースのデータを結合できます。

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) と SIEM の統合**- [Azure Event Hubs 統合を介して、Splunk、ArcSight、QRadar、Sumo Logic などの他の SIEM と Azure AD ログを統合できます](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。

* **[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security)** – アプリを検出し、アプリを管理し、すべてのアプリとリソースを制御し、クラウド アプリのコンプライアンス状況を確認できます。

監視とアラートの対象の多くは、条件付きアクセス ポリシーの影響です。 [条件付きアクセスに関する分析情報とレポートのブック](../conditional-access/howto-conditional-access-insights-reporting.md)を使用すると、サインイン時の 1 つ以上の条件付きアクセス ポリシーの影響と、デバイスの状態などのポリシーの結果を確認できます。 このブックを使用すると、影響の概要を表示し、特定の期間における影響を特定できます。 ブックを使用して、特定のユーザーのサインインを調査することもできます。 

この記事の残りの部分で、監視とアラートをお勧めする対象について説明し、脅威の種類別にまとめています。 特定の事前構築済みソリューションがある場合は、表の後にリンクを示すか、サンプルを提供しています。 それ以外の場合は、前述のツールを使用してアラートを作成できます。 

* **[Identity Protection](../identity-protection/overview-identity-protection.md)** - 調査に役立つ 3 つの主要なレポートを生成します。

   * **危険なユーザー** – リスクのあるユーザーに関する情報、検出の詳細、すべての危険なサインインの履歴、およびリスク履歴が含まれます。

   * **危険なサインイン** – 疑わしい状況を示す可能性のあるサインインの状況に関する情報が含まれています。 このレポートの情報を調査するための追加情報については、「[方法: リスクを調査する](../identity-protection/howto-identity-protection-investigate-risk.md)」をご覧ください。 

   *  **リスク検出** - サインインとユーザーのリスクを通知する Azure AD Identity Protection によって検出されたリスク シグナルに関する情報が含まれます。 詳細については、「[ユーザー アカウントの Azure AD セキュリティ操作ガイド](security-operations-user-accounts.md)」をご覧ください。

### <a name="data-sources-for-domain-controller-monitoring"></a>ドメイン コントローラーの監視のためのデータ ソース

最適な結果を得るために、Microsoft Defender for Identity を使用してドメイン コントローラーを監視することをお勧めします。 これにより、最良の検出と自動化の機能を使用できるようになります。 次のガイダンスに従ってください。

* [Microsoft Defender for Identity のアーキテクチャ](/defender-for-identity/architecture)

* [クイックスタート: Microsoft Defender for Identity を Active Directory に接続する](/defender-for-identity/install-step2)

Microsoft Defender for Identity を使用しない場合には、[イベント ログ メッセージ](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)を使用するか、[PowerShell コマンドレットを実行して](/windows-server/identity/ad-ds/deploy/troubleshooting-domain-controller-deployment)ドメイン コントローラーを監視できます。 

## <a name="components-of-hybrid-authentication"></a>ハイブリッド認証のコンポーネント

Azure ハイブリッド環境の一部として、次をベースラインとして使用し、監視とアラートの戦略に含める必要があります。 

* **PTA エージェント** – パススルー認証エージェントは、パススルー認証を有効にするために使用され、オンプレミスにインストールされます。 エージェントのバージョンの確認と次の手順の詳細については、「[Azure AD パススルー認証エージェント: バージョン リリース履歴](../hybrid/reference-connect-pta-version-history.md)」をご覧ください。 

* **AD FS/WAP** – Azure Active Directory フェデレーション サービス (Azure AD FS) と Web アプリケーション プロキシ (WAP) を使用すると、セキュリティおよびエンタープライズの境界を越えてデジタル ID と権利を安全に共有できます。 セキュリティのベストプラクティスについては、[Active Directoryフェデレーションサービスを保護するためのベストプラクティス](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)を参照してください。 

* **Azure AD Connect Health エージェント** – Azure AD Connect Health の通信リンクを提供するために使用されるエージェントです。 エージェントのインストールについては、「[Azure AD Connect Health エージェントのインストール](../hybrid/how-to-connect-health-agent-install.md)」をご覧ください。 

* **Azure AD Connect Sync Engine** - オンプレミスのコンポーネント。同期エンジンとも呼ばれます。 この機能の詳細については、「[Azure AD Connect 同期サービスの機能](../hybrid/how-to-connect-syncservice-features.md)」をご覧ください。

* **パスワード保護 DC エージェント** – Azure パスワード保護 DC エージェントは、イベント ログ メッセージの監視とレポート作成に使用されます。 詳細については、「[Active Directory Domain Services にオンプレミスの Azure AD パスワード保護を適用する](../authentication/concept-password-ban-bad-on-premises.md)」を参照してください。 

* **パスワード フィルター DLL** – DC エージェントのパスワード フィルター DLL は、オペレーティング システムからユーザーのパスワード検証要求を受け取ります。 このフィルターは、DC でローカルで実行されている DC エージェント サービスにそれらを転送します。 DLL の使用の詳細については、「[Active Directory Domain Services にオンプレミスの Azure AD パスワード保護を適用する](../authentication/concept-password-ban-bad-on-premises.md)」をご覧ください。 

* **パスワード ライトバック エージェント** - パスワード ライトバックは、[Azure AD Connect](../hybrid/whatis-hybrid-identity.md) により有効になる機能で、クラウド内でのパスワード変更を既存のオンプレミスのディレクトリにリアルタイムで書き戻せるようにします。 この機能の詳細については、「[Azure Active Directory でのセルフサービス パスワード リセットによる書き戻しのしくみ](../authentication/concept-sspr-writeback.md)」をご覧ください。

* **Azure AD アプリケーション プロキシ コネクタ** - オンプレミスの軽量エージェントで、アプリケーション プロキシ サービスへの送信接続を容易にします。 詳細については、「[Azure AD アプリケーション プロキシ コネクタを理解する](../app-proxy/application-proxy-connectors.md)」をご覧ください。 

## <a name="components-of-cloud-based-authentication"></a>クラウドベース認証のコンポーネント

Azure クラウドベース環境の一部として、次をベースラインとして使用し、監視とアラートの戦略に含める必要があります。

* **Azure AD アプリケーション プロキシ** – このクラウド サービスは、オンプレミスの Web アプリケーションへのセキュリティで保護されたリモート アクセスを提供します。 詳細については、「[Azure AD アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](../app-proxy/application-proxy-connectors.md)」をご覧ください。 

* **Azure AD Connect** – Azure AD Connect ソリューションのために使用されるサービス。 詳細については、「[Azure AD Connect とは](../hybrid/whatis-azure-ad-connect.md)」をご覧ください。

* **Azure AD Connect Health** – Service Health は、ユーザーが使用しているリージョン内の Azure サービスの正常性を追跡するカスタマイズ可能なダッシュボードを提供します。 詳細については、「[Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md)」をご覧ください。

* **Azure MFA** – Azure AD Multi-Factor Authentication では、ユーザーは認証のために複数の形式の証明を提供する必要があります。 これにより、環境をセキュリティで保護するためのプロアクティブな最初の手順が提供されます。 詳細については、「[動作のしくみ: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)」をご覧ください。

* **動的グループ** – Azure Active Directory (Azure AD) 管理者向けセキュリティ グループ メンバーシップの動的構成では、ユーザー属性に基づいて Azure AD で作成されるグループを設定する規則を設定できます。 詳しくは、「[動的グループと Azure Active Directory B2B コラボレーション](../external-identities/use-dynamic-groups.md)」を参照してください。

* **条件付きアクセス** - 条件付きアクセスは、Azure Active Directory で使用されるツールです。このツールによって、シグナルをまとめ、決定を行い、組織のポリシーを適用することができます。 条件付きアクセスは、新しい ID ドリブン コントロール プレーンの中心になるものです。 詳細については、「[条件付きアクセスとは](../conditional-access/overview.md)」をご覧ください。

* **Identity Protection** – 組織は、このツールを使用して、ID ベースのリスクの検出と修復の自動化、ポータルでのデータを使用したリスク調査、SIEM へのリスク検出データのエクスポートを実行できます。 詳細については、「[Identity Protection とは](../identity-protection/overview-identity-protection.md)」をご覧ください。

* **グループベースのライセンス** }– ライセンスは、ユーザーに直接割り当てるのではなく、グループに割り当てることができます。 ユーザーのライセンスの割り当て状態に関する情報は Azure AD に格納されます。 

* **プロビジョニング サービス** - プロビジョニングとは、ユーザーがアクセスする必要のあるクラウド アプリケーションのユーザー ID とロールを作成することです。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれます。 詳細については、「[Azure Active Directory でのアプリケーションのプロビジョニングのしくみ](../app-provisioning/how-provisioning-works.md)」をご覧ください。

* **Graph API** – RESTful Web API である Microsoft Graph API を使用すると、Microsoft Cloud サービスのリソースにアクセスできます。 アプリを登録し、ユーザーまたはサービスのための認証トークンを取得した後、Microsoft Graph API に要求を行うことができます。 詳しくは、「[Microsoft Graph の概要](/graph/overview)」をご覧ください。

* **Domain Service** – Azure Active Directory Domain Services (AD DS) では、ドメイン参加やグループ ポリシーなどのマネージド ドメイン サービスが提供されます。 詳細については、「[Azure Active Directory Domain Services とは](../../active-directory-domain-services/overview.md)」をご覧ください。

* **Azure Resource Manager** – Azure Resource Manager は、Azure のデプロイおよび管理サービスです。 お使いの Azure アカウント内のリソースを作成、更新、および削除できる管理レイヤーを提供します。 詳細については、「[Azure Resource Manager とは](../../azure-resource-manager/management/overview.md)」をご覧ください。

* **マネージド ID** – マネージド ID により、開発者は資格情報を管理する必要がなくなります。 マネージド ID は、Azure Active Directory (Azure AD) 認証をサポートするリソースに接続するときに使用する ID をアプリケーションに提供します。 詳細については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。

* **Privileged Identity Management** – Privileged Identity Management (PIM) は、お客様の組織内の重要なリソースへのアクセスを管理、制御、監視することができる、Azure Active Directory (Azure AD) のサービスです。 詳細については、「[Azure AD Privileged Identity Management とは](../privileged-identity-management/pim-configure.md)」を参照してください。

* **アクセス レビュー** - 組織で Azure Active Directory (Azure AD) アクセス レビューを使うことにより、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 詳細については、「[Azure AD アクセス レビューとは](../governance/access-reviews-overview.md)」をご覧ください。

* **エンタイトルメント管理** – Azure Active Directory (Azure AD) エンタイトルメント管理は、アクセス要求ワークフロー、アクセス割り当て、レビュー、および失効処理を自動化することで、ID とアクセスのライフサイクルを大規模に管理できる、[ID ガバナンス](../governance/identity-governance-overview.md)機能です。 詳細については、「[Azure AD エンタイトルメント管理とは](../governance/entitlement-management-overview.md)」を参照してください。

* **アクティビティ ログ** – アクティビティ ログは、Azure の [プラットフォーム ログ](../../azure-monitor/essentials/platform-logs-overview.md)であり、サブスクリプション レベルのイベントの分析情報をもたらします。 これには、リソースが変更されたときや仮想マシンが起動されたときなどの情報が含まれます。 詳細については、「[Azure アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)」をご覧ください。

* **セルフサービス パスワード リセット サービス** – Azure Active Directory (Azure AD) のセルフサービス パスワード リセット (SSPR) により、ユーザーは、管理者やヘルプ デスクが関与することなく、自分のパスワードを変更またはリセットできるようになります。 詳細については、[動作のしくみ:Azure AD のセルフサービス パスワード リセット のクイック スタート](../authentication/concept-sspr-howitworks.md)に関する記事をご覧ください。

* **デバイス サービス** – デバイス ID 管理は、[デバイス ベースの条件付きアクセス](../conditional-access/require-managed-devices.md)の基盤です。 デバイス ベースの条件付きアクセス ポリシーにより、環境内のリソースへのアクセスを確実にマネージド デバイスでのみ可能にすることができます。 詳細については、「[デバイス ID とは](../devices/overview.md)」をご覧ください。

* **セルフサービス グループ管理** – Azure Active Directory (Azure AD) では、独自のセキュリティ グループまたは Microsoft 365 グループを作成して管理することができます。 グループの所有者は、メンバーシップ要求を承認または拒否できます。また、グループ メンバーシップの制御を委任できます。 セルフサービスによるグループ管理機能は、メールを有効にしたセキュリティ グループまたは配布リストでは使用できません。 詳細については、「[Azure Active Directory でのセルフサービス グループ管理の設定](../enterprise-users/groups-self-service-management.md)」をご覧ください。

* **リスク検出** – リスクが検出されたときトリガーされる他のリスクに関する情報や、サインインの場所などの他の関連情報、および Microsoft Defender for Cloud Apps からの詳細情報が含まれます。

## <a name="next-steps"></a>次のステップ

これらのセキュリティ運用ガイドの記事を参照してください。

[Azure AD セキュリティ運用の概要](security-operations-introduction.md)

[ユーザー アカウントのためのセキュリティ運用](security-operations-user-accounts.md)

[特権アカウントのためのセキュリティ運用](security-operations-privileged-accounts.md)

[Privileged Identity Management のためのセキュリティ運用](security-operations-privileged-identity-management.md)

[アプリケーションのためのセキュリティ運用](security-operations-applications.md)

[デバイスのためのセキュリティ運用](security-operations-devices.md)

 
[インフラストラクチャのためのセキュリティ運用](security-operations-infrastructure.md)

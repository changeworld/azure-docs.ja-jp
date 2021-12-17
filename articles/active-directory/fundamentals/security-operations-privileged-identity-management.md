---
title: Privileged Identity Management に対する Azure Active Directory セキュリティ操作
description: ベースラインを確立し、Azure Active Directory Privileged Identity Management (PIM) を使用して、PIM で管理されているアカウントの潜在的な問題を監視し、警告する手順の説明。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ef4ca7858e50c43a2ed17656ecdd11c3affef6d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300806"
---
# <a name="azure-active-directory-security-operations-for-privileged-identity-management-pim"></a>Privileged Identity Management に対する Azure Active Directory (PIM) セキュリティ操作

ビジネス資産のセキュリティは、IT システムを管理する特権アカウントの整合性に依存します。 サイバー攻撃者は、資格情報の盗難攻撃を使用して、管理者アカウントやその他の特権アクセス アカウントをターゲットにし、機密データへのアクセスを試みます。

クラウド サービスの場合、防止と対応は、クラウド サービス プロバイダーと顧客の共同責任です。 

従来、組織のセキュリティは、セキュリティ境界としてネットワークの入口と出口のポイントに重点を置いていました。 しかし、SaaS アプリと個人用デバイスでは、この方法があまり効果的ではありません。 Azure Active Directory (Azure AD) では、組織の ID レイヤーでネットワーク セキュリティ境界を認証に置き換えます。 ユーザーが特権管理者ロールに割り当てられている場合、そのユーザーのアクセスはオンプレミス、クラウド、およびハイブリッド環境で保護されている必要があります。

お客様は、オンプレミスの IT 環境におけるすべての層のセキュリティに全責任を負います。 Azure クラウド サービスを使用する場合、防止と対応は、Microsoft (クラウド サービス プロバイダー) とお客様 (あなた) の共同責任となります。 

* 共有責任モデルの詳細については、「[クラウドにおける共有責任](../../security/fundamentals/shared-responsibility.md)」を参照してください。

* 特権を持つユーザーのアクセスをセキュリティで保護する方法の詳細については、「[Azure AD でのハイブリッドおよびクラウド デプロイのための特権アクセスのセキュリティ保護](../roles/security-planning.md)」を参照してください。

* 特権 ID の主要概念に関するさまざまな動画、ハウツーガイド、およびコンテンツについては、[Privileged Identity Management のドキュメント](../privileged-identity-management/index.yml)を参照してください。 

Privileged Identity Management (PIM) は Azure AD サービスの 1 つで、これにより、お客様の組織内の重要なリソースへのアクセスを管理、制御、監視できるようになります。 これらのリソースには、Azure AD、Azure、他の Microsoft Online Services (Microsoft 365 や Microsoft Intune など) 内のリソースが含まれます。 PIM を使用すると、次のリスクを軽減できます。

* セキュリティで保護された情報やリソースへのアクセス権を持つユーザーの数を特定し、最小限に抑えます。

* 機密リソースに対するアクセス許可の過剰、不要、または誤用を検出します。

* 悪意のあるアクターがセキュリティで保護された情報やリソースにアクセスする危険性を低減します。

* 未承認のユーザーが誤って機密性の高いリソースに影響を与える危険性を低減します。

この記事では、ベースラインの設定、特権アカウントのサインインと使用の監査、および特権アカウントの整合性を維持するために使用できる監査ログのソースについて説明します。 

## <a name="where-to-look"></a>どこを調べるか

調査と監視に使用するログ ファイルは次のとおりです。 

* [Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)

* [サインイン ログ](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 監査ログ](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault のログ](../../key-vault/general/logging.md?tabs=Vault)

Azure portal で、Azure AD 監査ログを表示したり、コンマ区切り値 (CSV) または JavaScript Object Notation (JSON) ファイルとしてダウンロードしたりできます。 Azure portal には、Azure AD ログを他のツールと統合する方法がいくつか用意されており、監視とアラートの自動化を強化することができます。

* [**Microsoft Sentinel**](../../sentinel/overview.md) – セキュリティ情報イベント管理 (SIEM) 機能を備え、エンタープライズ レベルでインテリジェントにセキュリティを分析します。 

* [**Azure Monitor**](../../azure-monitor/overview.md) - さまざまな条件に基づいて監視とアラートを自動化します。 ブックを作成または使用して、異なるソースのデータを結合できます。

* [**Azure Event Hubs**](../../event-hubs/event-hubs-about.md)**と SIEM の統合**- [Azure Event Hubs 統合を介して、Splunk、ArcSight、QRadar、Sumo Logic などの他の SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) と Azure AD ログを統合できます。

* [**Microsoft Defender for Cloud Apps**](/cloud-app-security/what-is-cloud-app-security) – アプリの検出と管理、アプリおよびリソース全体のガバナンス、クラウド アプリのコンプライアンス確認を行うことができます。 

この記事の残りの部分では、階層モデルを使用して編成された、監視およびアラートのベースラインを設定する際の推奨事項について説明します。 事前構築済みのソリューションへのリンクを次の表に示します。 前述のツールを使用してアラートを作成することもできます。 内容は、PIM の次のトピック領域で構成されています。

* ［基準］

* Azure AD ロールの割り当て 

* Azure AD ロールの割り当て設定

* Azure リソース ロールの割り当て

* Azure リソースのアクセス管理

* Azure サブスクリプションを管理するために昇格したアクセス権

## <a name="baselines"></a>［基準］

推奨されるベースライン設定は、次のとおりです。

| [What to monitor] (監視対象)| リスク レベル| 推奨| ロール| メモ |
| - |- |- |- |- |
| Azure AD ロールの割り当て| 高| <li>アクティベーションの理由が必須です。<li>アクティブ化の承認が必須です。<li>2 レベル承認者プロセスを設定します。<li>アクティブにすると、Azure Active Directory Multi-Factor Authentication (MFA) が必須になります。<li>最大昇格期間を 8 時間に設定します。| <li>特権ロール管理者<li>グローバル管理者| 特権ロール管理者は、適格なロール割り当てをアクティブ化しているユーザーの操作性を変更するなど、Azure AD 組織の PIM をカスタマイズできます。 |
| Azure リソース ロールの構成| 高| <li>アクティベーションの理由が必須です。<li>アクティブ化の承認が必須です。<li>2 レベル承認者プロセスを設定します。<li>アクティブにすると、Azure MFA が必須になります。<li>最大昇格期間を 8 時間に設定します。| <li>所有者<li>リソース管理者<li>ユーザー アクセス <li>管理者<li>グローバル管理者<li>セキュリティ管理者| 計画された変更でない場合は、すぐに調査します。 この設定により、攻撃者がユーザーの環境内の Azure サブスクリプションにアクセスできる可能性があります。 |


## <a name="azure-ad-roles-assignment"></a>Azure AD ロールの割り当て

特権ロール管理者は、Azure AD 組織の PIM をカスタマイズできます。 これには、次のように、適格ロール割り当てをアクティブ化しているユーザーのエクスペリエンスを変更することも含まれます。

* 不正なアクターによって Azure MFA の要件が削除されるのを防ぎ、特権アクセスをアクティブ化します。

* 悪意のあるユーザーが、特権アクセスをアクティブ化する理由と承認を回避するのを防ぎます。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 特権アカウントのアクセス許可に対する変更についてアラートを出し、その変更を追加します| 高| Azure AD 監査ログ| カテゴリ = ロール管理<br>および<br>アクティビティの種類 – 対象メンバーの追加 (永続的) <br>および<br>アクティビティの種類 – 対象メンバーの追加 (対象) <br>および<br>状態 = 成功/失敗<br>および<br>変更されたプロパティ = Role.DisplayName| 特権ロール管理者とグローバル管理者に対する変更を監視し、常に警告します。 <li>これは、攻撃者がロールの割り当て設定を変更する特権を取得しようとしていることを示している可能性があります<li> しきい値を定義していない場合は、ユーザーの場合については 60 分で 4 回、特権アカウントについては 60 分で 2 回でアラートを送信します。 |
| 特権アカウントのアクセス許可に対する一括削除の変更についてアラートを出します| 高| Azure AD 監査ログ| カテゴリ = ロール管理<br>および<br>アクティビティの種類 – 対象メンバーの削除 (永続的) <br>および<br>アクティビティの種類 – 対象メンバーの削除 (対象) <br>および<br>状態 = 成功/失敗<br>および<br>変更されたプロパティ = Role.DisplayName| 計画された変更でない場合は、すぐに調査します。 この設定により、攻撃者がユーザーの環境内の Azure サブスクリプションにアクセスできる可能性があります。 |
| PIM 設定の変更| 高| Azure AD 監査ログ| サービス = PIM<br>および<br>カテゴリ = ロール管理<br>および<br>アクティビティの種類 = PIM のロール設定の更新<br>および<br>ステータスの理由 = アクティブ化の MFA が無効になっている (例)| 特権ロール管理者とグローバル管理者に対する変更を監視し、常に警告します。 <li>これは、攻撃者がロールの割り当て設定を変更するために変更できるアクセス権を既に取得したことを示している可能性があります<li>これらのアクションの 1 つにより PIM 昇格のセキュリティが低下し、攻撃者が特権アカウントを取得しやすくなります。 |
| 昇格の承認と拒否| 高| Azure AD 監査ログ| サービス = アクセス レビュー<br>および<br>カテゴリ = ユーザー管理<br>および<br>アクティビティの種類 = 要求が承認/拒否された<br>および<br>開始したアクター = UPN| すべての昇格を監視する必要があります。 昇格が攻撃のタイムラインを明示している可能性があるので、すべての昇格をログに記録してください。 |
| アラート設定が無効に変更されました。| 高| Azure AD 監査ログ| サービス = PIM<br>および<br>カテゴリ = ロール管理<br>および<br>アクティビティの種類 = PIM アラートを無効にする<br>および<br>状態 = 成功/失敗| 常にアラート。 <li>不正なアクターが、特権アクセスをアクティブ化しようとして Azure MFA の要件に関連付けられているアラートを削除するのを検出できます。<li>疑わしいアクティビティまたは安全でないアクティビティの検出に役立ちます。 |


Azure AD 監査ログでロール設定の変更を識別する方法の詳細については、「[Privileged Identity Management で Azure AD ロールの監査履歴を表示する](../privileged-identity-management/pim-how-to-use-audit-log.md)」を参照してください。 

## <a name="azure-resource-role-assignment"></a>Azure リソース ロールの割り当て

Azure リソース ロールの割り当てを監視すると、リソースロールのアクティビティやアクティブ化を可視化できます。 これらは、リソースに対する攻撃面を作成するために悪用される可能性があります。 この種類のアクティビティを監視すると、次のアクティビティを検出できます。

* 特定のリソースでのクエリ ロールの割り当て

* すべての子リソースに対するロールの割り当て

* アクティブなロールと資格のあるロールの割り当ての変更

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 特権アカウント アクティビティのアラート リソース監査ログの監査| 高| PIM の [Azure リソース] にある [リソースの監査]| アクション: PIM での資格のあるメンバーのロールへの追加が完了しました (期限付き) <br>および<br>プライマリ ターゲット <br>および<br>種類: ユーザー<br>および<br>状態: 成功<br>| 常にアラート。 不正なアクターが、Azure のすべてのリソースを管理する資格のあるロールを追加しようとするのを検出できます。 |
| アラートの無効化に関する監査アラート リソース監査| Medium| PIM の [Azure リソース] にある [リソースの監査]| アクション: アラートの無効化<br>および<br>プライマリ ターゲット: 1 つのリソースに過剰に割り当てられた所有者<br>および<br>状態: 成功| 不正なアクターが、[アラート] ウィンドウでアラートを無効にし、悪意のあるアクティビティの調査をバイパスしようとするのを検出できます。 |
| アラートの無効化に関する監査アラート リソース監査| Medium| PIM の [Azure リソース] にある [リソースの監査]| アクション: アラートの無効化<br>および<br>プライマリ ターゲット: 1 つのリソースに過剰に割り当てられた永続的な所有者<br>および<br>状態: 成功| 不正なアクターが、[アラート] ウィンドウでアラートを無効にし、悪意のあるアクティビティの調査をバイパスしようとするのを防ぎきます |
| アラートの無効化に関する監査アラート リソース監査| Medium| PIM の [Azure リソース] にある [リソースの監査]| アクション: アラートの無効化<br>および<br>プライマリ ターゲット: 重複して作成されたロール<br>および<br>状態: 成功| 不正なアクターが、[アラート] ウィンドウでアラートを無効にし、悪意のあるアクティビティの調査をバイパスしようとするのを防ぎきます |


アラートの構成と、Azure リソース ロールの監査の詳細については、次を参照してください。

* [Privileged Identity Management で Azure リソース ロールに対するセキュリティ アラートを構成する](../privileged-identity-management/pim-resource-roles-configure-alerts.md)

* [Privileged Identity Management (PIM) で Azure リソース ロールの監査レポートを表示する](../privileged-identity-management/azure-pim-resource-rbac.md)

## <a name="access-management-for-azure-resources-and-subscriptions"></a>Azure リソースとサブスクリプションに対するアクセス管理

Azure AD でサブスクリプション管理を有効にする、所有者またはユーザー アクセス管理者サブスクリプション ロールに割り当てられたグループのユーザーまたはメンバー、および Azure AD 全体管理者には、既定でリソース管理者のアクセス許可が与えられます。 これらの管理者は、ロールの割り当て、ロール設定の構成、Azure リソース用 Privileged Identity Management (PIM) を使用したアクセスの確認ができます。 

リソース管理者のアクセス許可を持つユーザーは、リソースの PIM を管理できます。 そのため、この機能を使用して、仮想マシンやストレージ アカウントなどの Azure サブスクリプション リソースに対する特権アクセスを、不正なアクターに許可できてしまうというリスクの監視と軽減が必要になります。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 標高| 高| Azure AD の [管理] の [プロパティ]| 設定を定期的に確認します。<br>Azure リソースのアクセス管理| グローバル管理者は、Azure リソースのアクセス管理を有効にすることで昇格が可能です。<br>Active Directory に関連付けられているすべての Azure サブスクリプションと管理グループにロールを割り当てるアクセス許可が不正なアクターに付与されていないか確認します。 |


詳細については、「[Privileged Identity Management で Azure リソース ロールを割り当てる](../privileged-identity-management/pim-resource-roles-assign-roles.md)」を参照してください

## <a name="next-steps"></a>次のステップ
これらのセキュリティ運用ガイドの記事を参照してください。

[Azure AD セキュリティ運用の概要](security-operations-introduction.md)

[ユーザー アカウントのためのセキュリティ運用](security-operations-user-accounts.md)

[特権アカウントのためのセキュリティ運用](security-operations-privileged-accounts.md)

[Privileged Identity Management のためのセキュリティ運用](security-operations-privileged-identity-management.md)

[アプリケーションのためのセキュリティ運用](security-operations-applications.md)

[デバイスのためのセキュリティ運用](security-operations-devices.md)
 
[インフラストラクチャのためのセキュリティ運用](security-operations-infrastructure.md)

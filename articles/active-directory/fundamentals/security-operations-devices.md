---
title: デバイス向けの Azure Active Directory のセキュリティ運用
description: ベースラインを定めてデバイスの監視とレポートを行い、デバイスの潜在的なセキュリティ リスクを特定する方法について説明します。
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
ms.openlocfilehash: a80564d341ad6e3fbefe1500a222ad20fc3a482c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349283"
---
# <a name="azure-active-directory-security-operations-for-devices"></a>デバイス向けの Azure Active Directory のセキュリティ運用

デバイスが ID ベースの攻撃の対象にされることはあまりありません。しかし、セキュリティ制御への適合を偽装したり、ユーザーを偽装したりするために、デバイスが利用される "*可能性はあります*"。 デバイスと Azure AD との関係は次の 4 つのいずれかになります。 

* 登録解除済み

* [Azure Active Directory (Azure AD) 登録済み](../devices/concept-azure-ad-register.md)

* [Azure AD 参加済み](../devices/concept-azure-ad-join.md)

* [Hybrid Azure AD 参加済み](../devices/concept-azure-ad-join-hybrid.md)   
‎

登録済みデバイスおよび参加済みデバイスには、[プライマリ更新トークン (PRT)](../devices/concept-primary-refresh-token.md) が発行されます。PRT はプライマリ認証アーティファクトとして使用できるほか、多要素認証アーティファクトとして使用されることもあります。 攻撃者の行動としては、自身のデバイスを登録しようとする、正当なデバイスの PRT を使用してビジネス データにアクセスしようとする、正当なユーザー デバイスから PRT ベースのトークンを盗もうとする、Azure Active Directory のデバイスベースの制御に含まれる構成ミスを見つけようとする、などが考えられます。 Hybrid Azure AD 参加済みのデバイスについては、参加プロセスの開始と制御に管理者が対応するので、発生し得る攻撃方法を抑えることができます。

デバイスの統合方法の詳細については、「[Azure AD デバイスのデプロイを計画する](../devices/plan-device-deployment.md)」の「[統合方法を選択する](../devices/plan-device-deployment.md)」を参照してください。

悪意のあるアクターからデバイス経由でインフラストラクチャに攻撃を受けるリスクを減らすには、次のものを監視してください

* デバイス登録と Azure AD 参加

* アプリケーションにアクセスする非準拠デバイス

* BitLocker キーの取得

* デバイス管理者ロール

* 仮想マシンへのサインイン

## <a name="where-to-look"></a>確認すべき対象

調査と監視に使用するログ ファイルは次のとおりです。 

* [Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)

* [サインイン ログ](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 監査ログ](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault のログ](../..//key-vault/general/logging.md?tabs=Vault)

Azure AD 監査ログは、Azure portal で確認し、コンマ区切り値 (CSV) ファイルまたは JavaScript Object Notation (JSON) ファイルとしてダウンロードできます。 Azure portal には、Azure AD ログを他のツールと統合する方法がいくつか用意されており、監視とアラートの自動化を強化することができます。

* **[Microsoft Sentinel](../../sentinel/overview.md)** – セキュリティ情報イベント管理 (SIEM) 機能を備え、エンタープライズ レベルでインテリジェントにセキュリティを分析します。 

* **[Azure Monitor](../..//azure-monitor/overview.md)** - さまざまな条件に基づいて監視とアラートを自動化します。 ブックを作成または使用して、異なるソースのデータを結合できます。

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) と SIEM の統合** - Azure Event Hubs 統合を介して、Splunk、ArcSight、QRadar、Sumo Logic などの [他の SIEM と Azure AD ログを統合できます](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。

* **[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security)** – アプリを検出し、アプリを管理し、すべてのアプリとリソースを制御し、クラウド アプリのコンプライアンス状況を確認できます。 

監視およびアラートの対象となるのは、条件付きアクセス ポリシーの影響がほとんどです。 [条件付きアクセスに関する分析情報とレポート ブック](../conditional-access/howto-conditional-access-insights-reporting.md)を使用して、1 つまたは複数の条件付きアクセス ポリシーがサインインに及ぼしている影響と、デバイスの状態などのポリシーの結果を確認できます。 このブックでは、影響の概要を確認し、指定期間における影響を特定できます。 また、このブックを使用して、特定のユーザーのサインインを調査することもできます。 

 以降では、監視とアラートが推奨される対象について説明し、脅威の種類ごとの分類も示します。 特定の事前構築済みソリューションがある場合は、リンクを示すか、表の後にサンプルを提供しています。 それ以外の場合は、前述のツールを使用してアラートを作成できます。 

 ## <a name="device-registrations-and-joins-outside-policy"></a>ポリシー対象外のデバイスの登録と参加

Azure AD 登録済みデバイスおよび Azure AD 参加済みデバイスは、単一の認証要素に相当するプライマリ更新トークン (PRT) を保有しています。 これらのデバイスには、強力な認証要求が含まれることもあります。 PRT に強力な認証要求が含まれる状況の詳細については、「[PRT が MFA 要求を受けるのはいつですか?](../devices/concept-primary-refresh-token.md)」を参照してください。 悪意のあるアクターがデバイスの登録および参加を行えないようにするために、デバイスの登録または参加には多要素認証 (MFA) を必須としてください。 さらに、MFA なしで登録または参加したデバイスがないか監視してください。 また、MFA の設定とポリシーや、デバイス コンプライアンス ポリシーが変更されていないかどうかを監視する必要もあります。

 | [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| MFA なしで行われたデバイスの登録または参加| Medium| サインイン ログ| アクティビティ: デバイス登録サービスへの認証成功。 <br>および<br>MFA が必須とされていない| アラートのタイミング: <br>MFA なしでのデバイスの登録または参加 |
| Azure AD におけるデバイス登録の MFA のトグル設定に対する変更| 高| 監査ログ| アクティビティ: デバイス登録ポリシーの設定| 以下のものを探します。 <br>トグルがオフに設定されていないかどうか。 監査ログのエントリがない。 定期的なチェックをスケジュールしてください。 |
| ドメイン参加済みデバイスまたは準拠デバイスを必須とする条件付きアクセス ポリシーに対する変更。| 高| 監査ログ| CA ポリシーに対する変更<br>| アラートのタイミング: <br><li> ドメインへの参加または準拠を必須とするポリシーに対する変更。<li>信頼できる場所に対する変更。<li> MFA ポリシーの例外へのアカウントまたはデバイスの追加。 |


Microsoft Sentinel を使用し、MFA なしでデバイスを登録したとき、デバイスに参加したときに適切な管理者に通知するアラートを作成できます。

```
Sign-in logs

| where ResourceDisplayName == “Device Registration Service”

| where conditionalAccessStatus ==”success”

| where AuthenticationRequirement <> “multiFactorAuthentication”
```

また、[Microsoft Intune を使用してデバイス コンプライアンス ポリシーの設定および監視を行うこともできます](/mem/intune/protect/device-compliance-get-started)。

## <a name="non-compliant-device-sign-in"></a>非準拠デバイスのサインイン

条件付きアクセス ポリシーで準拠デバイスを必須とするだけでは、クラウド アプリケーションおよびソフトウェアとしてのサービス アプリケーションへのアクセスをブロックしきれないことがあります。 

Windows 10 デバイスのポリシー準拠を維持するには、[モバイル デバイス管理](/windows/client-management/mdm/) (MDM) が役立ちます。 Windows バージョン 1809 より、ポリシーの[セキュリティ ベースライン](/windows/client-management/mdm/)がリリースされています。 Azure Active Directory を [MDM と統合](/windows/client-management/mdm/azure-active-directory-integration-with-mdm)することで、企業ポリシーへの準拠をデバイスに義務付けると共に、デバイスの準拠状況をレポートできます。 

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 非準拠デバイスによるサインイン| 高| サインイン ログ| DeviceDetail.isCompliant ==false| 準拠デバイスからのサインインを必須とする場合、次のタイミングでアラートが送信されるようにしてください。<br><li> 非準拠デバイスによるサインイン。<li> MFA なしまたは信頼できる場所以外からのアクセス。<p>デバイスの必須化に取り組む場合、不審なサインインがないか監視してください。 |
| 不明なデバイスによるサインイン| 低| サインイン ログ| <li>DeviceDetail が空白<li>単一要素認証<li>アクセス元が信頼できない場所| 以下のものを探します。 <br><li>準拠デバイス以外からのアクセス。<li>MFA なしまたは信頼できる場所以外からのアクセス |


### <a name="use-loganalytics-to-query"></a>LogAnalytics を使用してクエリを実行する

**非準拠デバイスによるサインイン**

```
SigninLogs

| where DeviceDetail.isCompliant ==false

| where conditionalAccessStatus == “success”
```
 

**不明なデバイスによるサインイン**

```

SigninLogs
| where isempty(DeviceDetail.deviceId)

| where AuthenticationRequirement == "singleFactorAuthentication"

| where ResultType == "0"

| where NetworkLocationDetails == "[]"
```
 
## <a name="stale-devices"></a>古くなったデバイス

古くなったデバイスには、一定期間にわたりサインインしなかったデバイスが含まれます。 ユーザーが新しいデバイスを入手するかデバイスを紛失した場合や、Azure AD 参加済みデバイスがワイプまたは再プロビジョニングされた場合、デバイスは古くなったと見なされます。 また、ユーザーがテナントと関連付けられなくなった場合でも、デバイスは登録済みまたは参加済みの状態のままになる可能性があります。 古くなったデバイスは、そのプライマリ更新トークン (PRT) を使用できないように削除する必要があります。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 前回のサインイン日時| 低| Graph API| approximateLastSignInDateTime| Graph API または PowerShell を使用して、古くなったデバイスを特定し削除してください。 |

## <a name="bitlocker-key-retrieval"></a>BitLocker キーの取得

ユーザーのデバイスを侵害した攻撃者は、Azure AD で [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) キーを取得する可能性があります。 ユーザーがキーを取得することはめったにないため、このような事態について監視および調査する必要があります。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| キーの取得| Medium| 監査ログ| OperationName == [Read BitLocker key]\(BitLocker キーの読み取り\)| 調査項目 <br><li>キーの取得<li> キーを取得したユーザーによる他の異常な行動。 |


LogAnalytics で次のようなクエリを作成します。

```
AuditLogs

| where OperationName == "Read BitLocker key” 
```

## <a name="device-administrator-roles"></a>デバイス管理者ロール

グローバル管理者およびクラウド デバイス管理者には、すべての Azure AD 参加済みデバイスに対するローカル管理者権限が自動で付与されます。 環境の安全を保つには、これらの権限を持つユーザーを監視することが重要です。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| グローバル管理者ロールまたはデバイス管理者ロールに追加されたユーザー| 高| 監査ログ| アクティビティの種類 = ロールへのメンバーの追加。| 以下のものを探します。<li> これらの Azure AD ロールへの新規ユーザーの追加。<li> 以降に発生したマシンまたはユーザーの異常な行動。 |


## <a name="non-azure-ad-sign-ins-to-virtual-machines"></a>Azure AD 以外からの仮想マシンへのサインイン

Windows または Linux 仮想マシン (VM) へのサインインについて、Azure AD アカウント以外のアカウントによるサインインがないか監視してください。

### <a name="azure-ad-sign-in-for-linux"></a>Linux への Azure AD サインイン

組織は Linux への Azure AD サインインを使用することで、Azure AD アカウントを使用し Secure Shell プロトコル (SSH) 経由で Azure Linux VM にサインインできます。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| Azure AD アカウント以外からのサインイン (特に SSH 経由)| 高| ローカル認証ログ| Ubuntu:  <br>‎/var/log/auth.log で SSH の使用状況を監視する<br>Red Hat: <br>‎/var/log/sssd/ で SSH の使用状況を監視する| 以下のものを探します。<li> [Azure AD 以外のアカウントが VM への接続に成功した](../devices/howto-vm-sign-in-azure-ad-linux.md)ことを示すエントリ。 <li>次の例を参照してください。 |


Ubuntu の例:

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: Version: 1.0.015570001; user: localusertest01 (5 月 9 日 23:49:39 ubuntu1804 aad_certhandler[3915]: バージョン: 1.0.015570001; ユーザー: localusertest01)

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: User 'localusertest01' is not an AAD user; returning empty result. (5 月 9 日 23:49:39 ubuntu1804 aad_certhandler[3915]: ユーザー 'localusertest01' は AAD ユーザーではありません; 空の結果が返されました。)

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: Version: 1.0.015570001; user: localusertest01 (5 月 9 日 23:49:43 ubuntu1804 aad_certhandler[3916]: バージョン: 1.0.015570001; ユーザー: localusertest01)

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: User 'localusertest01' is not an AAD user; returning empty result. (5 月 9 日 23:49:43 ubuntu1804 aad_certhandler[3916]: ユーザー 'localusertest01' は AAD ユーザーではありません; 空の結果が返されました。)

   May 9 23:49:43 ubuntu1804 sshd[3909]: Accepted publicly for localusertest01 from 192.168.0.15 port 53582 ssh2: RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ (5 月 9 日 23:49:43 ubuntu1804 sshd[3909]: 192.168.0.15 ポート 53582 ssh2 からの localusertest01 について公的に受け入れられました: RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ)

   May 9 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session): session opened for user localusertest01 by (uid=0). (5 月 9 日 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session): ユーザー localusertest01 に対してセッションが (uid=0) で開かれました。)

Linux VM へのサインインに対するポリシーを設定し、未承認のローカル アカウントが追加された Linux VM を検出してフラグを設定できます。 詳細については、「[Azure Policy を使用して、標準および評価コンプライアンスを確保する](../devices/howto-vm-sign-in-azure-ad-linux.md)」を参照してください。 

### <a name="azure-ad-sign-ins-for-windows-server"></a>Windows Server への Azure AD サインイン

組織は、Windows への Azure AD サインインを使用することで、Azure AD アカウントを使用しリモート デスクトップ プロトコル (RDP) 経由で Windows 2019 以降の Azure VM にサインインできます。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| Azure AD アカウント以外からのサインイン (特に RDP 経由)| 高| Windows Server イベント ログ| Windows VM への対話型ログイン| イベント 528、ログオンの種類 10 (RemoteInteractive)。<br>ターミナル サービスまたはリモート デスクトップ経由でユーザーがサインインしたときに表示されます。 |


## <a name="next-steps"></a>次の手順

次のセキュリティ運用ガイドの記事もご覧ください。

[Azure AD セキュリティ運用の概要](security-operations-introduction.md)

[ユーザー アカウントのためのセキュリティ運用](security-operations-user-accounts.md)

[特権アカウントのためのセキュリティ運用](security-operations-privileged-accounts.md)

[Privileged Identity Management のためのセキュリティ運用](security-operations-privileged-identity-management.md)

[アプリケーションのためのセキュリティ運用](security-operations-applications.md)

[デバイスのためのセキュリティ運用](security-operations-devices.md)

 
[インフラストラクチャのためのセキュリティ運用](security-operations-infrastructure.md)

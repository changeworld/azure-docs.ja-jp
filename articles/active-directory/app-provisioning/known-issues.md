---
title: Azure Active Directory でのアプリケーション プロビジョニングの既知の問題
description: Azure Active Directory で自動アプリケーション プロビジョニングを使用する場合の既知の問題について説明します。
author: kenwith
ms.author: kenwith
manager: karenh444
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.reviewer: arvinh
ms.openlocfilehash: 76789c36e935e03a2677054e9a318291ebd149f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028027"
---
# <a name="known-issues-for-application-provisioning-in-azure-active-directory"></a>Azure Active Directory でのアプリケーション プロビジョニングの既知の問題
この記事では、アプリのプロビジョニングを操作する場合に注意する必要がある既知の問題について説明します。 UserVoice のアプリケーション プロビジョニング サービスに関するフィードバックを提供するには、[Azure Active Directory (Azure AD) のアプリケーションのプロビジョニングの UserVoice](https://aka.ms/appprovisioningfeaturerequest) ページを参照してください。 Microsoft では、サービスを改善するために UserVoice を注意深く確認しています。

> [!NOTE]
> この記事は既知の問題の包括的な一覧ではありません。 一覧にない問題をご存じの場合は、ページの下部でフィードバックを提供してください。

## <a name="authorization"></a>承認 

#### <a name="unable-to-save"></a>保存できない

保存するには、テナントの URL、シークレット トークン、通知メールを入力する必要があります。 1 つだけを指定することはできません。 

#### <a name="unable-to-change-provisioning-mode-back-to-manual"></a>プロビジョニング モードを手動に変更することができない

プロビジョニングを初めて構成すると、プロビジョニング モードが手動から自動に切り替わることがわかります。 手動に戻すことはできません。 ただし、UI を使用してプロビジョニングをオフにすることはできます。 UI でプロビジョニングをオフにすると、ドロップダウンを手動に設定する場合と同様に動作します。

## <a name="attribute-mappings"></a>属性マッピング 

#### <a name="attribute-samaccountname-or-usertype-not-available-as-a-source-attribute"></a>属性 SamAccountName または userType をソース属性として使用できない

属性 **SamAccountName** および **userType** は、既定ではソース属性として使用できません。 スキーマを拡張して属性を追加してください。 スキーマを拡張すると、使用できるソース属性の一覧に属性を追加することができます。 詳細については、[ソース属性の欠落い](user-provisioning-sync-attributes-for-mapping.md)に関するページを参照してください。 

#### <a name="source-attribute-dropdown-missing-for-schema-extension"></a>スキーマ拡張のソース属性ドロップダウンがない

UI のソース属性ドロップダウンにスキーマの拡張機能が表示されない場合があります。 属性マッピングの詳細設定に移動し、手動で属性を追加します。 詳細については、[属性マッピングのカスタマイズ](customize-application-attributes.md)に関するページを参照してください。

#### <a name="null-attribute-cant-be-provisioned"></a>null 属性をプロビジョニングできない

現在、Azure AD によって null 属性をプロビジョニングすることはできません。 ユーザー オブジェクトの属性が null の場合は、スキップされます。 

#### <a name="maximum-characters-for-attribute-mapping-expressions"></a>属性マッピング式の最大文字数

属性マッピング式には、最大 10,000 文字を使用できます。 

#### <a name="unsupported-scoping-filters"></a>サポートされていないスコープ フィルター

ディレクトリ拡張機能と、**appRoleAssignments**、**userType**、および **accountExpires** 属性は、スコープ フィルターとしてはサポートされていません。

#### <a name="multivalue-directory-extensions"></a>複数値ディレクトリ拡張機能

複数値ディレクトリ拡張機能は、属性マッピングまたはスコープ フィルターでは使用できません。 

## <a name="service-issues"></a>サービスに関する問題 

#### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

- パスワードのプロビジョニングはサポートされていません。 
- 入れ子になったグループのプロビジョニングはサポートされていません。 
- テナントのサイズにより、B2C テナントへのプロビジョニングはサポートされていません。
- すべてのクラウドですべてのプロビジョニング アプリが使用できるわけではありません。 たとえば、Atlassian は、政府機関向けクラウドではまだ利用できません。 アプリ開発者と連携して、アプリをすべてのクラウドにオンボードしています。

#### <a name="automatic-provisioning-isnt-available-on-my-oidc-based-application"></a>OIDC ベースのアプリケーションで自動プロビジョニングを使用できない

アプリの登録を作成した場合、エンタープライズ アプリ内の対応するサービス プリンシパルが、自動ユーザー プロビジョニングで有効になりません。 ギャラリーにアプリを追加するように要求するか (複数の組織での使用を想定している場合)、プロビジョニング用にギャラリー以外の 2 つ目のアプリを作成する必要があります。

#### <a name="the-provisioning-interval-is-fixed"></a>プロビジョニングの間隔が固定されている

プロビジョニング サイクル間の[時間](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)は現在構成できません。 

#### <a name="changes-not-moving-from-target-app-to-azure-ad"></a>変更がターゲット アプリから Azure AD に反映されない

アプリのプロビジョニング サービスでは、外部アプリで行われた変更が認識されません。 そのため、ロールバックするアクションは行われません。 アプリのプロビジョニング サービスは、Azure AD で行われた変更に依存しています。 

#### <a name="switching-from-sync-all-to-sync-assigned-not-working"></a>[すべて同期] から [割り当てられた同期] への切り替えが機能しない

**[すべて同期]** から **[割り当てられた同期]** にスコープを変更したら、変更を有効にするために必ず再起動も実行します。 再起動は UI から実行できます。

#### <a name="provisioning-cycle-continues-until-completion"></a>プロビジョニング サイクルが完了するまで続行される

`enabled = off` のプロビジョニングを設定するか、 **[停止]** を選択すると、現在のプロビジョニング サイクルが完了するまで実行は続行されます。 プロビジョニングを再び有効にするまで、サービスによって今後のサイクルの実行が停止されます。

#### <a name="member-of-group-not-provisioned"></a>グループのメンバーがプロビジョニングされない

グループがスコープ内にあり、メンバーがスコープ外にある場合、グループはプロビジョニングされます。 スコープ外のユーザーはプロビジョニングされません。 メンバーがスコープ内に戻っても、サービスではすぐに変更が検出されません。 プロビジョニングを再開すると、問題が解決されます。 すべてのユーザーが適切にプロビジョニングされるようにするため、サービスを定期的に再起動してください。

#### <a name="manager-isnt-provisioned"></a>マネージャーがプロビジョニングされない

ユーザーとそのマネージャーが両方ともプロビジョニング対象となっている場合は、ユーザーがプロビジョニングされた後でマネージャーが更新されます。 初日に、ユーザーがプロビジョニング対象でマネージャーが対象外となっている場合、マネージャーを参照せずにユーザーがプロビジョニングされます。 その後マネージャーがプロビジョニング対象となっても、プロビジョニングを再開し、サービスによってすべてのユーザーがもう一度評価されるまで、マネージャーの参照は更新されません。 

## <a name="on-premises-application-provisioning"></a>オンプレミス アプリケーションのプロビジョニング
次の情報は、Azure AD ECMA コネクタ ホストとオンプレミス アプリケーションのプロビジョニングに関する既知の制限事項の最新の一覧です。

### <a name="application-and-directories"></a>アプリケーションとディレクトリ
次のアプリケーションとディレクトリは、まだサポートされていません。

#### <a name="active-directory-domain-services-user-or-group-writeback-from-azure-ad-by-using-the-on-premises-provisioning-preview"></a>Active Directory Domain Services (オンプレミスのプロビジョニング プレビューを使用した Azure AD からのユーザーまたはグループの書き戻し)
   - ユーザーが Azure AD Connect によって管理されている場合、権限のあるソースはオンプレミスの Azure AD です。 そのため、Azure AD 内でユーザー属性を変更することはできません。 このプレビューでは、Azure AD Connect によって管理されるユーザーの権限のソースは変更されません。
   - Azure AD Connect とオンプレミスのプロビジョニングを使用して Active Directory Domain Services にグループまたはユーザーをプロビジョニングしようとすると、ループが作成される可能性があります。この場合、Azure AD Connect はクラウドのプロビジョニング サービスによって行われた変更を上書きできます。 Microsoft では、グループまたはユーザーの書き戻し専用の機能に取り組んでいます。 [この Web サイト](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789/)で UserVoice のフィードバックに賛成投票して、プレビューの状態を追跡します。 または、Azure AD から Active Directory へのユーザーまたはグループの書き戻しに [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) を使用することもできます。

#### <a name="connectors-other-than-sql"></a>SQL 以外のコネクタ

   Azure AD ECMA コネクタ ホストは、汎用 SQL コネクタで正式にサポートされています。 Web サービス コネクタやカスタム ECMA コネクタなどの他のコネクタを使用することはできますが、"*まだサポートされていません*"。

#### <a name="azure-ad"></a>Azure AD

   オンプレミスのプロビジョニングを使用することにより、既に Azure AD にあるユーザーを利用して、サードパーティのアプリケーションにプロビジョニングすることができます。 *サードパーティ製アプリケーションのディレクトリにユーザーを持ち込むことはできません。* お客様は、ネイティブの HR 統合、Azure AD Connect、Microsoft Identity Manager、または Microsoft Graph を利用して、ユーザーをディレクトリに取り込む必要があります。

### <a name="attributes-and-objects"></a>属性とオブジェクト 
次の属性とオブジェクトはサポートされていません。
   - 複数値の属性。
   - 参照属性 (manager など)。
   - グループ。
   - 複雑なアンカー (ObjectTypeName+UserName など)。
   - オンプレミスのアプリケーションは Azure AD とフェデレーションされないことがあり、ローカル パスワードが必要になることがあります。 オンプレミスのプロビジョニング プレビューでは、Azure AD とサードパーティのアプリケーション間での "*ワンタイム パスワードのプロビジョニングまたはパスワードの同期はサポートされていません*"。
   - **export_password** 仮想属性、**SetPassword**、および **ChangePassword** 操作はサポートされていません。

#### <a name="ssl-certificates"></a>SSL 証明書の数
   現在、Azure AD ECMA コネクタ ホストでは、Azure によって信頼されている SSL 証明書、またはプロビジョニング エージェントを使用する必要があります。 証明書のサブジェクトは Azure AD ECMA コネクタ ホストがインストールされているホスト名と一致する必要があります。

#### <a name="anchor-attributes"></a>アンカー属性
   現在、Azure AD ECMA コネクタ ホストでは、アンカー属性の変更 (名前の変更) またはアンカーを形成するには複数の属性が必要となるターゲット システムはサポートされていません。 

#### <a name="attribute-discovery-and-mapping"></a>属性の検出とマッピング
   ターゲット アプリケーションによってサポートされる属性が検出され、Azure portal の **[属性マッピング]** に表示されます。 新しく追加された属性は引き続き検出されます。 属性の型が変更されており (たとえば、文字列からブール値)、属性がマッピングの一部である場合、型は Azure portal 内で自動的に変更されません。 ユーザーは [マッピング] の [詳細設定] にアクセスし、属性の種類を手動で更新する必要があります。

## <a name="next-steps"></a>次のステップ
[プロビジョニングのしくみ](how-provisioning-works.md)

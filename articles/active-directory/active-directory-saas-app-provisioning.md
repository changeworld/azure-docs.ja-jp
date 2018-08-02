---
title: Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化 | Microsoft Docs
description: Azure AD を使用して、複数のサードパーティ SaaS アプリケーション間でユーザー アカウントを自動的にプロビジョニング、プロビジョニング解除、継続的に更新する方法の紹介。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 9a0b89528adc173fa3aa26415942cbef81dcb291
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364208"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS アプリへのユーザー プロビジョニングの自動化とは
Azure Active Directory (Azure AD) を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**以下に、この機能を使用して行うことができる例をいくつか示します。**

* 新しいユーザーがチームまたは組織に加わるときに、適切なシステムで新しいアカウントを自動的に作成できます。
* ユーザーがチームまたは組織を離れるときに、適切なシステムでアカウントを自動的に非アクティブ化できます。
* アプリとシステムの ID は、ディレクトリ (人事システム) の変更に基づいて最新の状態に保たれることが保証されます。
* ユーザー以外のオブジェクト (グループなど) をサポートするアプリケーションにそれらをプロビジョニングします。

**自動化されたユーザー プロビジョニングには、次の機能も含まれています。**

* ソース システムとターゲット システムとの間で既存の ID を一致させる機能。
* ソース システムからターゲット システムにフローするユーザー データを定義する、カスタマイズ可能な属性マッピング。
* プロビジョニング エラーの電子メール通知 (オプション)
* 監視とトラブルシューティングに役立つレポートとアクティビティ ログ。

## <a name="why-use-automated-provisioning"></a>自動プロビジョニングを使用する理由
この機能を使用する一般的な動機は、次のとおりです。

* 手動によるプロビジョニング プロセスに関連するコスト、非効率性、および人的エラーを回避する。
* 独自に開発したプロビジョニング ソリューションやプロビジョニング スクリプトのホスティングとメンテナンスに伴うコストを回避する。
* ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高めるため。
* 多くのユーザーを特定の SaaS アプリまたは SaaS システムに簡単にインポートするため。
* 誰をプロビジョニングし、誰がアプリにサインインできるようにするかを、1 つのポリシー セットで決定する利便性を享受するため。

## <a name="how-does-automatic-provisioning-work"></a>自動プロビジョニングのしくみ
    
**Azure AD プロビジョニング サービス**は、SaaS アプリや他のシステムに対してユーザーをプロビジョニングするために、各アプリケーション ベンダーから提供されるユーザー管理 API エンドポイントに接続します。 これらのユーザー管理 API エンドポイントを使用すると、Azure AD ではプログラムによってユーザーを作成、更新、削除できます。 また、一部の限られたアプリケーションについては、ID に関連したその他のオブジェクト (グループ、ロールなど) をプロビジョニング サービスで作成、更新、削除することができます。 

![プロビジョニング](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*図 1: Azure AD プロビジョニング サービス*

![出力方向のプロビジョニング](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*図 2: Azure AD から主要 SaaS アプリケーションへの "出力方向" のユーザー プロビジョニング ワークフロー*

![入力方向のプロビジョニング](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*図 3: 主要な人材管理 (HCM) アプリケーションから Azure Active Directory および Windows Server Active Directory への "入力方向" のユーザー プロビジョニング ワークフロー*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム

Azure AD は、一般に普及しているさまざまな SaaS アプリや人事管理システムとの連携にあらかじめ対応しているほか、SCIM 2.0 標準の特定の領域を実装するアプリにも広く対応しています。

### <a name="pre-integrated-applications"></a>事前統合されたアプリケーション
Azure AD が事前統合プロビジョニング コネクタをサポートするすべてのアプリケーションの一覧については、[ユーザー プロビジョニングのためのアプリケーション チュートリアルの一覧](saas-apps/tutorial-list.md)を参照してください。

他のアプリのプロビジョニングのサポートを要求するために Azure AD エンジニア リング チームに問い合わせる場合は、[Azure Active Directory フィードバック フォーラム](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)からメッセージを送信してください。

> [!NOTE]
> アプリで自動ユーザー プロビジョニングをサポートするには、まず、外部プログラム上でのユーザーの作成、保守、削除の自動化の実現に必要なユーザー管理 API を提供する必要があります。 そのため、すべての SaaS アプリがこの機能と互換性があるとは限りません。 ユーザー管理 API をサポートするアプリでは、Azure AD エンジニア リング チームがこれらのアプリケーションにプロビジョニング コネクタを作成できるようになります。この作業の優先順位は現在の顧客と見込み顧客のニーズによって決まります。 

### <a name="connecting-applications-that-support-scim-20"></a>SCIM 2.0 をサポートするアプリケーションの接続
SCIM 2.0 ベースのユーザー管理 API を実装するアプリケーションを汎用的に接続する方法については、[SCIM を使用した Azure Active Directory からアプリケーションへのユーザーおよびグループの自動プロビジョニング](manage-apps/use-scim-to-provision-users-and-groups.md)に関するページをご覧ください。

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>アプリケーションへの自動プロビジョニングを設定する方法

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

選択したアプリケーション用の Azure AD プロビジョニング サービスの構成は、**[Azure Portal](https://portal.azure.com)** から開始します。 **[Azure Active Directory] > [エンタープライズ アプリケーション]** セクションで **[追加]** を選択し、**[すべて]** を選択した後、実際のシナリオに応じて次のいずれかを追加します。

* **[注目のアプリケーション]** セクションに表示されるアプリケーションすべてが自動プロビジョニングに対応しています。 追加のアプリケーションについては、[ユーザー プロビジョニングのためのアプリケーション チュートリアルの一覧](saas-apps/tutorial-list.md)を参照してください。

* 独自開発の SCIM 統合には、[ギャラリー以外のアプリケーション] オプションを使用します。

![[ギャラリー]](./media/active-directory-saas-app-provisioning/gallery.png)

プロビジョニングの構成は、アプリケーション管理画面の **[プロビジョニング]** タブで行います。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* Azure AD プロビジョニング サービスには、アプリケーションが提供するユーザー管理 API に接続できるように**管理者資格情報**を指定する必要があります。 このセクションでは、資格情報でエラーが発生した場合、またはプロビジョニング ジョブが[検疫](#quarantine)に移行した場合の電子メール通知を有効にすることもできます。

* ソース システム (例: Azure AD) のどのフィールドの内容をターゲット システム (例: ServiceNow) のどのフィールドと同期させるかを指定する**属性マッピング**を構成できます。 ターゲット アプリケーションが対応していれば、ユーザー アカウントだけでなく、必要に応じてグループのプロビジョニングもこのセクションで構成することができます。 システム間のアカウントの照合に使用するフィールドは、[Matching properties]\(一致するプロパティ\) で選択できます。 "[式](active-directory-saas-writing-expressions-for-attribute-mappings.md)" を使用すると、ソース システムから取得した値をターゲット システムに書き込む前に変更または変換できます。 詳細については、[属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)に関するページを参照してください。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **スコープ フィルター**により、ターゲット システムへのプロビジョニング/プロビジョニング解除の対象となるソース システム内のユーザーとグループをプロビジョニング サービスに指定します。 スコープ フィルターでは、次の 2 つの側面を総合的に評価してプロビジョニング対象ユーザーが決定されます。

    * **属性の値に基づくフィルター** - 属性マッピングの [ソース オブジェクト スコープ] メニューで、特定の属性値に基づいてフィルター処理できます。 たとえば、"Department" 属性が "Sales" であるユーザーのみをプロビジョニングの対象として指定することができます。 詳細については、[スコープ フィルターの使用](active-directory-saas-scoping-filters.md)に関するページをご覧ください。

    * **割り当てに基づくフィルター** - "割り当て済み" のユーザーとグループだけをプロビジョニングの対象とするか、Azure AD ディレクトリ内のすべてのユーザーをプロビジョニングの対象とするかを、ポータルの [プロビジョニング] の [設定] セクションにある [スコープ] メニューで指定できます。 ユーザーとグループの "割り当て" の詳細については、「[Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる](manage-apps/assign-user-or-group-access-portal.md)」を参照してください。
    
* 実行中であるかどうかを含め、アプリケーションのプロビジョニング サービスの動作は、**[設定]** で制御します。

* **[監査ログ]** には、Azure AD プロビジョニング サービスによって実行された各操作の記録が表示されます。 詳細については、[プロビジョニング レポートに関するガイド](active-directory-saas-provisioning-reporting.md)を参照してください。

![設定](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> Azure AD ユーザー プロビジョニング サービスは、[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) を使用して構成および管理することもできます。


## <a name="what-happens-during-provisioning"></a>プロビジョニング中の動作

Azure AD がソース システムである場合、プロビジョニング サービスは、[Azure AD Graph API の差分クエリ機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)を使用してユーザーとグループを監視します。 プロビジョニング サービスは、ソース システムとターゲット システムに対して初期同期を実行した後、増分同期を定期的に行います。 

### <a name="initial-sync"></a>初期同期
プロビジョニング サービスが開始されたときに行われる最初の同期では、次の処理が実行されます。

1. ソース システムのすべてのユーザーとグループにクエリを実行し、[属性マッピング](active-directory-saas-customizing-attribute-mappings.md)で定義されているすべての属性を取得します。
2. 返されたユーザーおよびグループを、構成済み[割り当て](manage-apps/assign-user-or-group-access-portal.md)または[属性ベースのスコープ フィルター](active-directory-saas-scoping-filters.md)を使用してフィルター処理します。
3. ユーザーが割り当て済み、またはプロビジョニング対象の場合、サービスはターゲット システムに対してクエリを実行し、指定された[照合属性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)を使用して、一致するユーザーがいないかどうかを確認します。 たとえば、ソース システムの userPrincipal 名が一致属性であり、ターゲット システムの userName にマップされる場合、プロビジョニング サービスは、ターゲット システムにクエリを実行し、ソース システムの userPrincipal 名の値と一致する userName がないかどうかを確認します。
4. 一致するユーザーがターゲット システムで見つからない場合、ソース システムから返された属性を使用してユーザーが作成されます。
5. 一致するユーザーが見つかった場合、そのユーザーは、ソース システムによって提供された属性を使用して更新されます。
6. 属性マッピングに "参照" 属性が含まれている場合、サービスは、ターゲット システムで追加の更新を実行して参照先オブジェクトを作成し、リンクします。 たとえば、あるユーザーがターゲット システムで "Manager" 属性を持ち、それがターゲット システムで作成された別のユーザーにリンクされている場合があります。
7. 初期同期の終了時に基準値を保持します。これは、以降の増分同期の始点になります。

ServiceNow、Google Apps、Box など、アプリケーションの中には、ユーザーのプロビジョニングだけでなく、グループとそのメンバーのプロビジョニングをサポートしているものがあります。 このような場合、グループのプロビジョニングが[マッピング](active-directory-saas-customizing-attribute-mappings.md)で有効になっていると、プロビジョニング サービスは、ユーザーとグループを同期したうえで、グループ メンバーシップを同期します。 

### <a name="incremental-syncs"></a>増分同期
初期同期後に行われるすべての同期で、次の処理が実行されます。

1. ソース システムにクエリを実行し、前回の基準値が保存された後に更新されたユーザーおよびグループがないかどうかを確認します。
2. 返されたユーザーおよびグループを、構成済み[割り当て](manage-apps/assign-user-or-group-access-portal.md)または[属性ベースのスコープ フィルター](active-directory-saas-scoping-filters.md)を使用してフィルター処理します。
3. ユーザーが割り当て済み、またはプロビジョニング対象の場合、サービスはターゲット システムに対してクエリを実行し、指定された[照合属性](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties)を使用して、一致するユーザーがいないかどうかを確認します。
4. 一致するユーザーがターゲット システムで見つからない場合、ソース システムから返された属性を使用してユーザーが作成されます。
5. 一致するユーザーが見つかった場合、そのユーザーは、ソース システムによって提供された属性を使用して更新されます。
6. 属性マッピングに "参照" 属性が含まれている場合、サービスは、ターゲット システムで追加の更新を実行して参照先オブジェクトを作成し、リンクします。 たとえば、あるユーザーがターゲット システムで "Manager" 属性を持ち、それがターゲット システムで作成された別のユーザーにリンクされている場合があります。
7. 以前プロビジョニングの対象だったユーザーが対象から外れた場合 (割り当てられていない場合を含む)、サービスは、更新を通じてターゲット システムのユーザーを無効にします。
8. 以前プロビジョニングの対象だったユーザーが、ソース システムで無効にされた場合、または論理削除された場合、サービスは、更新によってターゲット システムのユーザーを無効にします。
9. 以前プロビジョニングの対象だったユーザーが、ソース システムから物理的に削除された場合、サービスは、ターゲット システムのユーザーを削除します。 Azure AD では、論理削除されたユーザーは、削除されてから 30 日後に物理的に削除されます。
10. 増分同期の終了時に新しい基準値を保持します。これは、以降の増分同期の始点になります。

>[!NOTE]
> [[属性マッピング]](active-directory-saas-customizing-attribute-mappings.md) セクションの **[対象オブジェクトのアクション]** チェック ボックスを使用して、作成、更新、または削除操作を必要に応じて無効にできます。 更新中にユーザーを無効にするロジックは、"accountEnabled" などのフィールドの属性マッピングでも制御されます。

プロビジョニング サービスは、バックツーバック増分同期を、次のいずれかのイベントが発生するまで、[各アプリケーションに固有のチュートリアル](saas-apps/tutorial-list.md)で定義された間隔で無期限に実行し続けます。

* Azure Portal または適切な Graph API コマンドを使用してサービスが手動で停止された 
* Azure Portal の **[Clear state and restart]\(状態をクリアして再起動\)** オプション、または適切な Graph API コマンドを使用して、新しい初期同期がトリガーされた。 これにより、保存された基準値がクリアされ、すべてのソース オブジェクトが再評価されます。
* 属性マッピングまたはスコープ フィルターの変更によって、新しい初期同期がトリガーされた。 この場合も、保存された基準値がクリアされ、すべてのソース オブジェクトが再評価されます。
* 高いエラー率によりプロビジョニング プロセスが検疫に移行し (以下を参照)、そのまま 4 週間を超えて検疫にとどまっている。 この場合、サービスは自動的に無効になります。

### <a name="errors-and-retries"></a>エラーと再試行 
ターゲット システムでのエラーが原因で個々のユーザーの追加、更新、または削除がターゲットシステム上で行えない場合、その操作は次の同期サイクルで再試行されます。 ユーザーの再試行が失敗し続けると、その頻度は減少し始め、1 日 1 回になるように段階的にスケール バックします。 エラーを解決するには、管理者が[監査ログ](active-directory-saas-provisioning-reporting.md)で "プロセス エスクロー" イベントがないかどうかを確認し、根本原因を特定して、適切な対応を取る必要があります。 たとえば、次のような一般的なエラーがあります。

* ターゲット システムで必要な属性がソース システムのユーザーに設定されていない
* ソース システムのユーザーの属性値に対して、ターゲット システムに一意の制約があり、同じ値が他のユーザー レコードに存在する

このようなエラーを解決するには、ソース システムで影響を受けるユーザーの属性値を調整するか、競合が発生しないように属性マッピングを調整します。   

### <a name="quarantine"></a>検疫
ターゲット システムに対する呼び出しのほとんどまたはすべてが、常にエラー (管理者の資格情報が無効である場合など) が原因で失敗する場合、プロビジョニング ジョブは "検疫" 状態になります。 これは[プロビジョニング概要レポート](active-directory-saas-provisioning-reporting.md)、または電子メール (電子メール通知が Azure Portal で構成されている場合) で示されます。 

検疫に移行すると、増分同期の頻度は徐々に減少し、最終的には 1 日 1 回になります。 

問題を引き起こしているすべてのエラーが修正されたら、プロビジョニング ジョブは検疫から削除され、次の同期サイクルが開始します。 プロビジョニング ジョブが検疫にとどまっている期間が 4 週間を超えると、そのプロビジョニング ジョブは無効になります。


## <a name="how-long-will-it-take-to-provision-users"></a>ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか。

前のセクションで説明したように、プロビジョニング ジョブで実行されているのが初期同期か増分同期かによって、パフォーマンスが異なります。

**初期同期**では、ジョブ時間は、プロビジョニングのスコープ内のユーザーおよびグループの数、ソース システム内のユーザーおよびグループの合計数などさまざまな要因によって異なります。 初期同期のパフォーマンスに影響する要因の包括的な一覧はこのセクションで後程まとめます。

**増分同期**では、ジョブ時間は、その同期サイクルで検出される変更の数によって異なります。 ユーザーまたはグループのメンバーシップの変更が 5,000 未満の場合、ジョブは 1 増分同期サイクル内で終了できます。 

次の表は、一般的なプロビジョニング シナリオの同期時間のまとめです。 これらのシナリオでは、ソース システムが Azure AD、ターゲット システムが SaaS アプリです。 同期時間は、SaaS アプリである ServiceNow、Workplace、Salesforce、および Google Apps の同期ジョブの統計分析から導かれています。


| スコープ構成 | スコープ内のユーザー、グループ、およびメンバー | 初期同期時間 | 増分同期時間 |
| -------- | -------- | -------- | -------- |
| 割り当てられたユーザーとグループのみを同期する |  1,000 未満 |  30 分未満 | 30 分未満 |
| 割り当てられたユーザーとグループのみを同期する |  1,000 ～ 10,000 | 142 ～ 708 分 | 30 分未満 |
| 割り当てられたユーザーとグループのみを同期する |   10,000 ～ 100,000 | 1,170 ～ 2,340 分 | 30 分未満 |
| Azure AD のすべてのユーザーとグループを同期する |  1,000 未満 | 30 分未満  | 30 分未満 |
| Azure AD のすべてのユーザーとグループを同期する |  1,000 ～ 10,000 | 30 分未満 ～ 120 分 | 30 分未満 |
| Azure AD のすべてのユーザーとグループを同期する |  10,000 ～ 100,000  | 713 ～ 1,425 分 | 30 分未満 |
| Azure AD のすべてのユーザーを同期する|  1,000 未満  | 30 分未満 | 30 分未満 |
| Azure AD のすべてのユーザーを同期する | 1,000 ～ 10,000  | 43 ～ 86 分 | 30 分未満 |


**割り当てられたユーザーとグループのみを同期する**の構成では、次の式を使用して**初期同期**のおよその最小予測時間と最大予測時間を確認できます。

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
**初期同期**の完了に要する時間に影響する要因のまとめ

* プロビジョニングのスコープ内のユーザーとグループの合計数

* ソース システム (Azure AD) に存在するユーザー、グループ、およびグループのメンバーの合計数

* プロビジョニングのスコープ内のユーザーがターゲット アプリケーション内の既存のユーザーと一致するかどうか、または新たに作成する必要があるかどうか。 すべてのユーザーを初めて作成する同期ジョブはすべてのユーザーが既存ユーザーと一致する同期ジョブの約 *2 倍*の時間がかかります。

* [監査ログ](active-directory-saas-provisioning-reporting.md)内のエラー数。 多くのエラーが発生し、プロビジョニング サービスが検疫状態に移行した場合、パフォーマンスは低下します   

* ターゲット システムによって実装される要求レートの制限および調整。 ターゲット システムによって、要求レートの制限および調整が実装される場合があり、大規模な同期動作中にパフォーマンスに影響する可能性があります。 このような条件下では、高速で大量の要求を受信するアプリは応答レートが遅くなったり、接続が閉じたりする場合があります。 パフォーマンスを向上するために、アプリが処理できるよりも速くアプリ要求を送信しないようにコネクタによって調整する必要があります。 Microsoft がビルドしたプロビジョニング コネクタはこの調整を行います。 

* 割り当てられたグループの数とサイズ。 割り当てられたグループの同期はユーザーの同期よりも時間がかかります。 割り当てられたグループの数とサイズの両方がパフォーマンスに影響します。 [グループ オブジェクト同期用に有効にされたマッピング](active-directory-saas-customizing-attribute-mappings.md#editing-group-attribute-mappings)がアプリにある場合、グループ名やメンバーシップなどのグループ プロパティがユーザーの他に同期されます。 これらの追加の同期はユーザー オブジェクトの同期のみの場合よりも時間がかかります。


##<a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>ユーザーが正しくプロビジョニングされているかどうかを確認する方法

ユーザー プロビジョニング サービスによって実行された操作はすべて、Azure AD の監査ログに記録されます。 これには、ソース システムとターゲット システムに対して実行されたすべての読み取りおよび書き込み操作と、各操作の際に読み取られたり書き込まれたりしたユーザー データが含まれます。

Azure Portal で監査ログを確認する方法については、[プロビジョニング レポートに関するガイド](active-directory-saas-provisioning-reporting.md)をご覧ください。


##<a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>ユーザー プロビジョニングに関する問題のトラブルシューティング方法

自動ユーザー プロビジョニングをトラブルシューティングする方法のシナリオ ベースのガイダンスについては、「[アプリケーションに対するユーザーの構成およびプロビジョニングに関する問題](active-directory-application-provisioning-content-map.md)」を参照してください。


##<a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>自動ユーザー プロビジョニングを展開するためのベスト プラクティス

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

アプリケーションに対する出力方向のユーザー プロビジョニングを展開するための段階的なデプロイ計画の例については、[ユーザー プロビジョニングのための ID デプロイ ガイド](https://aka.ms/userprovisioningdeploymentplan)/をご覧ください。


## <a name="related-articles"></a>関連記事
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](saas-apps/tutorial-list.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Azure AD 同期 API の概要](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

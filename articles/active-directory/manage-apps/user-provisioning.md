---
title: Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化 | Microsoft Docs
description: Azure AD を使用して、複数のサードパーティ SaaS アプリケーション間でユーザー アカウントを自動的にプロビジョニング、プロビジョニング解除、継続的に更新する方法の紹介。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144558"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Azure Active Directory でのアプリケーションに対するユーザー プロビジョニングとプロビジョニング解除を自動化する

Azure Active Directory (Azure AD) での**アプリ プロビジョニング**という用語は、ユーザーがアクセスする必要のあるクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションにおいてユーザーの ID とロールを自動的に作成することを意味します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれます。 一般的なシナリオには、[Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)、[Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial)、[ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) などのアプリケーションへの Azure AD ユーザーのプロビジョニングが含まれます。

![プロビジョニングの概要図](media/user-provisioning/provisioning-overview.png)

この機能を使用すると次のことができます。

- **プロビジョニングを自動化する:** 新しいユーザーがチームまたは組織に加わるときに、適切なシステムで新しいアカウントを自動的に作成できます。
- **プロビジョニング解除を自動化する:** ユーザーがチームまたは組織を離れるときに、適切なシステムでアカウントを自動的に非アクティブ化できます。
- **システム間でデータを同期する:** アプリとシステムの ID は、ディレクトリ (人事システム) での変更に基づいて最新の状態に保たれることが保証されます。
- **グループをプロビジョニングする:** グループをサポートするアプリケーションにグループをプロビジョニングします。
- **アクセスを管理する:** アプリケーションにプロビジョニングされたユーザーの監視と監査を行います。
- **ブラウン フィールドのシナリオでシームレスにデプロイする:** ターゲット システムにユーザーが既に存在する場合でも、システム間で既存の ID を一致させ、簡単に統合できるようにします。
- **リッチなカスタマイズを使用する:** ソース システムからターゲット システムにフローする必要があるユーザー データが定義されている、カスタマイズ可能な属性マッピングを利用します。
- **重大なイベントのアラートを受け取る:** プロビジョニング サービスでは、重大なイベントに対するアラートが提供され、ビジネス ニーズに合わせてカスタム アラートを定義できる Log Analytics の統合が可能になります。

## <a name="benefits-of-automatic-provisioning"></a>自動プロビジョニングの利点

現代の組織では使用されるアプリケーションの数が増え続けており、IT 管理者は大規模なアクセス管理を強いられます。 Security Assertion Markup Language (SAML) や Open ID Connect (OIDC) などの標準を使用すると、管理者はシングル サインオン (SSO) をすばやく設定できますが、アクセスするにはユーザーをアプリにプロビジョニングする必要もあります。 多くの管理者にとって、プロビジョニングとは、すべてのユーザー アカウントを手動で作成すること、または毎週 CSV ファイルをアップロードすることを意味しますが、これらのプロセスは時間がかかり、コストがかかり、エラーが発生しやすくなります。 プロビジョニングを自動化するために SAML Just-In-Time (JIT) などのソリューションが採用されていますが、企業では、ユーザーが組織からいなくなる場合や、役割の変更のために特定のアプリにアクセスする必要がなくなる場合に、ユーザーのプロビジョニングを解除するためのソリューションも必要です。

自動プロビジョニングを使用する一般的な動機には、次のようなものがあります。

- プロビジョニング プロセスの効率と正確さを最大限に高める。
- 独自に開発したプロビジョニング ソリューションやプロビジョニング スクリプトのホスティングとメンテナンスに伴うコストを抑える。
- ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高める。
- 多くのユーザーを特定の SaaS アプリまたは SaaS システムに簡単にインポートする。
- 誰をプロビジョニングし、誰がアプリにサインインできるようにするかを、1 つのポリシー セットで決定する。

Azure AD のユーザー プロビジョニングは、これらの課題に対応するのに役立ちます。 お客様による Azure AD ユーザー プロビジョニングの使用方法について詳しくは、[ASOS のケース スタディ](https://aka.ms/asoscasestudy)をご覧ください。 次のビデオでは、Azure AD でのユーザー プロビジョニングの概要について説明します。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム

Azure AD は、一般的な多くの SaaS アプリや人事管理システムとの連携にあらかじめ対応しているほか、[SCIM 2.0 標準](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)の特定の領域を実装するアプリにも広く対応しています。

* **事前統合されたアプリケーション (ギャラリー SaaS アプリ)** 。 Azure AD で事前統合プロビジョニング コネクタがサポートされているすべてのアプリケーションについては、[ユーザー プロビジョニングのためのアプリケーション チュートリアルの一覧](../saas-apps/tutorial-list.md)をご覧ください。 ギャラリーに一覧表示されている事前統合アプリケーションでは、通常、プロビジョニングに SCIM 2.0 ベースのユーザー管理 API が使用されています。 

   ![Salesforce のロゴ](media/user-provisioning/gallery-app-logos.png)

   プロビジョニングのために新しいアプリケーションを要求したい場合は、[アプリケーションをアプリ ギャラリーと統合するよう要求する](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)ことができます。 ユーザー プロビジョニング要求の場合、アプリケーションには SCIM 準拠のエンドポイントが必要です。 アプリをプラットフォームに迅速にオンボードできるよう、アプリケーションのベンダーに SCIM 標準に準拠するよう要求してください。

* **SCIM 2.0 をサポートするアプリケーション**。 SCIM 2.0 ベースのユーザー管理 API が実装されているアプリケーションを汎用的に接続する方法については、[SCIM を使用した Azure Active Directory からアプリケーションへのユーザーおよびグループの自動プロビジョニング](use-scim-to-provision-users-and-groups.md)に関するページをご覧ください。

## <a name="what-is-scim"></a>SCIM とは

プロビジョニングとプロビジョニング解除の自動化を助けるため、アプリでは独自のユーザー API とグループ API を公開します。 ただし、複数のアプリでユーザーを管理しようとすると、すべてのアプリで同じ単純な操作 (ユーザーの作成や更新、ユーザーのグループへの追加、ユーザーのプロビジョニング解除など) が実行されることがわかります。 ただし、これらの単純な操作のすべてが、異なるエンドポイント パス、異なるユーザー情報指定方法、および情報の各要素を表す異なるスキーマを使用して、少しずつ異なる方法で実装されています。

これらの課題に対処するため、SCIM 仕様では、ユーザーのアプリへの移動、アプリからの移動、アプリ内での移動に対し、共通のユーザー スキーマが提供されています。 SCIM は、プロビジョニングに対する事実上の標準になりつつあり、SAML や OpenID Connect などのフェデレーション標準と組み合わせて使用すると、エンドツーエンドの標準ベースのアクセス管理用ソリューションが管理者に提供されます。

アプリケーションに対するユーザーとグループのプロビジョニングおよびプロビジョニング解除を自動化するための SCIM の使用について詳しくは、「[Azure Active Directory での SCIM のユーザー プロビジョニング](use-scim-to-provision-users-and-groups.md)」をご覧ください。

## <a name="how-does-automatic-provisioning-work"></a>自動プロビジョニングのしくみ

**Azure AD プロビジョニング サービス**は、SaaS アプリや他のシステムに対してユーザーをプロビジョニングするために、各アプリケーション ベンダーから提供されるユーザー管理 API エンドポイントに接続します。 これらのユーザー管理 API エンドポイントを使用すると、Azure AD ではプログラムによってユーザーを作成、更新、削除できます。 また、一部の限られたアプリケーションについては、ID に関連したその他のオブジェクト (グループ、ロールなど) をプロビジョニング サービスで作成、更新、削除することができます。

![Azure AD プロビジョニング サービス](./media/user-provisioning/provisioning0.PNG)
*図 1: Azure AD プロビジョニング サービス*

![出力方向のユーザー プロビジョニング ワークフロー](./media/user-provisioning/provisioning1.PNG)
*図 2: Azure AD から主要 SaaS アプリケーションへの "出力方向" のユーザー プロビジョニング ワークフロー*

![入力方向のユーザー プロビジョニング ワークフロー](./media/user-provisioning/provisioning2.PNG)
*図 3: 主要な人材管理 (HCM) アプリケーションから Azure Active Directory および Windows Server Active Directory への "入力方向" のユーザー プロビジョニング ワークフロー*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>アプリケーションへの自動プロビジョニングを設定する方法

ギャラリーに一覧表示される事前統合アプリケーションについては、自動プロビジョニングの設定すに関するステップバイステップのガイダンスが用意されています。 [統合ギャラリー アプリのチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/saas-apps/)を参照してください。 次のビデオでは、SalesForce の自動ユーザー プロビジョニングの設定方法が説明されています。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0 をサポートする他のアプリケーションについては、記事「[Azure Active Directory での SCIM のユーザー プロビジョニング](use-scim-to-provision-users-and-groups.md)」の手順に従ってください。

## <a name="what-happens-during-provisioning"></a>プロビジョニング中の動作

Azure AD がソース システムである場合、プロビジョニング サービスは、[Azure AD Graph API の差分クエリ機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)を使用してユーザーとグループを監視します。 プロビジョニング サービスは、ソース システムとターゲット システムに対して初回サイクルを実行した後、増分サイクルを定期的に行います。

### <a name="initial-cycle"></a>初回サイクル

プロビジョニング サービスが開始されたときに行われる最初の同期では、次の処理が実行されます。

1. ソース システムのすべてのユーザーとグループにクエリを実行し、[属性マッピング](customize-application-attributes.md)で定義されているすべての属性を取得します。
1. 返されたユーザーおよびグループを、構成済み[割り当て](assign-user-or-group-access-portal.md)または[属性ベースのスコープ フィルター](define-conditional-rules-for-provisioning-user-accounts.md)を使用してフィルター処理します。
1. ユーザーが割り当て済み、またはプロビジョニング対象の場合、サービスはターゲット システムに対してクエリを実行し、指定された[照合属性](customize-application-attributes.md#understanding-attribute-mapping-properties)を使用して、一致するユーザーがいないかどうかを確認します。 例:ソース システムの userPrincipal 名が一致属性であり、ターゲット システムの userName にマップされる場合、プロビジョニング サービスは、ターゲット システムにクエリを実行し、ソース システムの userPrincipal 名の値と一致する userName がないかどうかを確認します。
1. 一致するユーザーがターゲット システムで見つからない場合、ソース システムから返された属性を使用してユーザーが作成されます。 ユーザー アカウントが作成された後、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 一致するユーザーが見つかった場合、そのユーザーは、ソース システムによって提供された属性を使用して更新されます。 ユーザー アカウントが照合された後、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 属性マッピングに "参照" 属性が含まれている場合、サービスは、ターゲット システムで追加の更新を実行して参照先オブジェクトを作成し、リンクします。 たとえば、あるユーザーがターゲット システムで "Manager" 属性を持ち、それがターゲット システムで作成された別のユーザーにリンクされている場合があります。
1. 初回サイクルの終了時に基準値を保持します。これは、以降の増分サイクルの始点になります。

ServiceNow、G Suite、Box など、アプリケーションの中には、ユーザーのプロビジョニングだけでなく、グループとそのメンバーのプロビジョニングをサポートしているものがあります。 このような場合、グループのプロビジョニングが[マッピング](customize-application-attributes.md)で有効になっていると、プロビジョニング サービスは、ユーザーとグループを同期したうえで、グループ メンバーシップを同期します。

### <a name="incremental-cycles"></a>増分サイクル

初回サイクルの後、他のすべてのサイクルでは以下が行われます。

1. ソース システムにクエリを実行し、前回の基準値が保存された後に更新されたユーザーおよびグループがないかどうかを確認します。
1. 返されたユーザーおよびグループを、構成済み[割り当て](assign-user-or-group-access-portal.md)または[属性ベースのスコープ フィルター](define-conditional-rules-for-provisioning-user-accounts.md)を使用してフィルター処理します。
1. ユーザーが割り当て済み、またはプロビジョニング対象の場合、サービスはターゲット システムに対してクエリを実行し、指定された[照合属性](customize-application-attributes.md#understanding-attribute-mapping-properties)を使用して、一致するユーザーがいないかどうかを確認します。
1. 一致するユーザーがターゲット システムで見つからない場合、ソース システムから返された属性を使用してユーザーが作成されます。 ユーザー アカウントが作成された後、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 一致するユーザーが見つかった場合、そのユーザーは、ソース システムによって提供された属性を使用して更新されます。 新しく割り当てられたアカウントが照合された場合、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 属性マッピングに "参照" 属性が含まれている場合、サービスは、ターゲット システムで追加の更新を実行して参照先オブジェクトを作成し、リンクします。 たとえば、あるユーザーがターゲット システムで "Manager" 属性を持ち、それがターゲット システムで作成された別のユーザーにリンクされている場合があります。
1. 以前プロビジョニングの対象だったユーザーが対象から外れた場合 (割り当てられていない場合を含む)、サービスは、更新を通じてターゲット システムのユーザーを無効にします。
1. 以前プロビジョニングの対象だったユーザーが、ソース システムで無効にされた場合、または論理削除された場合、サービスは、更新によってターゲット システムのユーザーを無効にします。
1. 以前プロビジョニングの対象だったユーザーが、ソース システムから物理的に削除された場合、サービスは、ターゲット システムのユーザーを削除します。 Azure AD では、論理削除されたユーザーは、削除されてから 30 日後に物理的に削除されます。
1. 増分サイクルの終了時に新しい基準値を保持します。これは、以降の増分サイクルの始点になります。

> [!NOTE]
> [[マッピング]](customize-application-attributes.md) セクションの **[対象オブジェクトのアクション]** チェック ボックスを使用して、 **[作成]** 、 **[更新]** 、または **[削除]** 操作を必要に応じて無効にできます。 更新中にユーザーを無効にするロジックは、"accountEnabled" などのフィールドの属性マッピングでも制御されます。

プロビジョニング サービスは、バックツーバック増分サイクルを、次のいずれかのイベントが発生するまで、[各アプリケーションに固有のチュートリアル](../saas-apps/tutorial-list.md)で定義された間隔で無期限に実行し続けます。

- Azure Portal または適切な Graph API コマンドを使用してサービスが手動で停止された 
- Azure portal の **[Clear state and restart]\(状態を消去して再開\)** オプション、または適切な Graph API コマンドを使用して、新しい初回サイクルがトリガーされた。 この操作では、保存された基準値がクリアされ、すべてのソース オブジェクトが再評価されます。
- 属性マッピングまたはスコープ フィルターの変更によって、新しい初回サイクルがトリガーされた。 また、この操作では、保存された基準値がクリアされ、すべてのソース オブジェクトが再評価されます。
- 高いエラー率によりプロビジョニング プロセスが検疫に移行し (以下を参照)、そのまま 4 週間を超えて検疫にとどまっている。 この場合、サービスは自動的に無効になります。

### <a name="errors-and-retries"></a>エラーと再試行

ターゲット システムでのエラーが原因で個々のユーザーの追加、更新、または削除がターゲットシステム上で行えない場合、その操作は次の同期サイクルで再試行されます。 ユーザーの再試行が失敗し続けると、その頻度は減少し始め、1 日 1 回になるように段階的にスケール バックします。 エラーを解決するには、管理者が[プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)を確認し、根本原因を特定して、適切な対応を取る必要があります。 たとえば、次のような一般的なエラーがあります。

- ターゲット システムで必要な属性がソース システムのユーザーに設定されていない
- ソース システムのユーザーの属性値に対して、ターゲット システムに一意の制約があり、同じ値が他のユーザー レコードに存在する

このようなエラーを解決するには、ソース システムで影響を受けるユーザーの属性値を調整するか、競合が発生しないように属性マッピングを調整します。

### <a name="quarantine"></a>検疫

ターゲット システムに対する呼び出しのほとんどまたはすべてが、常にエラー (管理者の資格情報が無効である場合など) が原因で失敗する場合、プロビジョニング ジョブは "検疫" 状態になります。 この状態は、[プロビジョニング概要レポート](check-status-user-account-provisioning.md)、または電子メール (電子メール通知が Azure portal で構成されている場合) で示されます。

検疫状態であると、増分サイクルの頻度は徐々に減少し、最終的には 1 日 1 回になります。

問題を引き起こしているすべてのエラーが修正されたら、プロビジョニング ジョブは検疫から削除され、次の同期サイクルが開始します。 プロビジョニング ジョブが検疫にとどまっている期間が 4 週間を超えると、そのプロビジョニング ジョブは無効になります。 検疫の状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。

## <a name="how-long-will-it-take-to-provision-users"></a>ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか。

プロビジョニング ジョブで実行されているのが初期プロビジョニング サイクルか増分サイクルかによって、パフォーマンスが異なります。 プロビジョニングにかかる時間、およびプロビジョニング サービスの状態を監視する方法について詳しくは、[ユーザー プロビジョニングの状態の確認](application-provisioning-when-will-provisioning-finish-specific-user.md)に関するページをご覧ください。

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>ユーザーが正しくプロビジョニングされているかどうかを確認する方法

ユーザー プロビジョニング サービスによって実行された操作はすべて、Azure AD の[プロビジョニング ログ (プレビュー)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) に記録されます。 これには、ソース システムとターゲット システムに対して実行されたすべての読み取りおよび書き込み操作と、各操作の際に読み取られたり書き込まれたりしたユーザー データが含まれます。

Azure portal でプロビジョニング ログを確認する方法については、[プロビジョニング レポートに関するガイド](check-status-user-account-provisioning.md)を参照してください。

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>ユーザー プロビジョニングに関する問題のトラブルシューティング方法

自動ユーザー プロビジョニングをトラブルシューティングする方法のシナリオ ベースのガイダンスについては、「[アプリケーションに対するユーザーの構成およびプロビジョニングに関する問題](application-provisioning-config-problem.md)」を参照してください。

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>自動ユーザー プロビジョニングを展開するためのベスト プラクティス

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

アプリケーションに対する出力方向のユーザー プロビジョニングを展開するための段階的なデプロイ計画の例については、[ユーザー プロビジョニングのための ID デプロイ ガイド](https://aka.ms/deploymentplans/userprovisioning)をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>SaaS アプリへの自動ユーザー プロビジョニングは、Azure AD の B2B ユーザーに対応しますか。

はい、Azure AD ユーザー プロビジョニング サービスを使って、Azure AD の B2B (またはゲスト) ユーザーを SaaS アプリケーションにプロビジョニングすることは可能です。

ただし、B2B ユーザーが、Azure AD を使用して SaaS アプリケーションにサインインするには、SaaS アプリケーションで、SAML ベースのシングル サインオン機能が特定の方法で構成されている必要があります。 B2B ユーザーからのサインインをサポートするように SaaS アプリケーションを構成する方法の詳細については、「[B2B コラボレーション用の SaaS アプリの構成]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)」を参照してください。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>SaaS アプリへの自動ユーザー プロビジョニングは、Azure AD の動的グループに対応しますか。

はい。 "割り当てられているユーザーおよびグループのみを同期する" ように構成されている場合、Azure AD ユーザー プロビジョニング サービスは、SaaS アプリケーションのユーザーを、[動的グループ](../users-groups-roles/groups-create-rule.md)のメンバーであるかどうかに基づいて、プロビジョニングまたはプロビジョニング解除できます。 動的グループは、「すべてのユーザーとグループを同期する」オプションにも対処します。

ただし、動的グループの使用は、Azure AD から SaaS アプリケーションへのエンドツーエンドのユーザー プロビジョニングのパフォーマンス全体に影響することがあります。 動的グループを使用している場合は、これらの注意事項と推奨事項に留意してください。

- SaaS アプリケーションで動的グループのユーザーをプロビジョニングまたはプロビジョニング解除する速度は、動的グループがメンバーシップの変更を評価する速さによって決まります。 動的グループの処理状態を確認する方法の詳細については、「[メンバーシップ ルールの処理状態をチェックする](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)」を参照してください。

- 動的グループを使用しているときに、メンバーシップの喪失はプロビジョニング解除イベントになるので、ユーザープロビジョニングおよびプロビジョニング解除に留意してルールを慎重に考慮する必要があります。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>SaaS アプリへの自動ユーザー プロビジョニングは、Azure AD の入れ子になった動的グループに対応しますか。

No. "割り当てられたユーザーとグループのみを同期する" ように構成されている場合、Azure AD ユーザー プロビジョニング サービスは、入れ子になったグループに含まれているユーザーを読み取ったりプロビジョニングしたりすることができません。 明示的に割り当てられたグループの直接のメンバーであるユーザーだけを読み取ってプロビジョニングできます。

これは、「アプリケーションへのグループベースの割り当て」の制限であり、シングル サインオンにも影響し、「[SaaS アプリケーションへのアクセスをグループで管理する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )」で説明しています。

対処法として、プロビジョニングする必要のあるユーザーを含んだグループを明示的に割り当てる (またはそれ以外の場合では[スコープ](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)する) 必要があります。

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Azure AD とターゲット アプリケーションの間のプロビジョニングでは、暗号化されたチャネルを使用していますか。

はい。 サーバー ターゲットに HTTPS SSL 暗号化を使用しています。

## <a name="related-articles"></a>関連記事

- [SaaS アプリを統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
- [ユーザー プロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
- [属性マッピングの式の書き方](functions-for-customizing-application-data.md)
- [ユーザー プロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM を使用して、Azure AD からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同期 API の概要](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

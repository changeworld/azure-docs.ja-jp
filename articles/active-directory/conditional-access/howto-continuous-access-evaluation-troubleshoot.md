---
title: Azure AD の継続的アクセス評価を使用してサインインの監視とトラブルシューティングを行う
description: Azure AD の継続的アクセス評価を使用して、ユーザー状態の変化に対するトラブルシューティングと対応を迅速に行います
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu, shreyamalik
ms.collection: M365-identity-device-management
ms.openlocfilehash: 292860ed26a39205f221c213f392b4b120006d13
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344771"
---
# <a name="monitor-and-troubleshoot-continuous-access-evaluation"></a>継続的アクセス評価の監視とトラブルシューティング

管理者は、[継続的アクセス評価 (CAE)](concept-continuous-access-evaluation.md) を複数の方法で適用して、サインイン イベントの監視とトラブルシューティングを行うことができます。

## <a name="continuous-access-evaluation-sign-in-reporting"></a>継続的アクセス評価のサインイン レポート

管理者は、CAE を適用してユーザーのサインインを監視できます。 このペインは、次の手順に従って表示できます。

1.  **Azure portal** に、条件付きアクセス管理者、セキュリティ管理者、または全体管理者としてサインインします。
1.  **[Azure Active Directory]**  >  **[サインイン]** の順に移動します。 
1.  **[Is CAE Token]\(CAE トークンである\)** フィルターを適用します。 

[ ![サインイン ログにフィルターを追加して、CAE が適用されている場所を確認します](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

ここでは、管理者にユーザーのサインイン イベントに関する情報が表示されます。 サインインを選択すると、適用されている条件付きアクセス ポリシーや、CAE が有効になっているかなど、セッションの詳細が表示されます。 

認証ごとに複数のサインイン要求が存在します。 対話型タブに表示されるものもあれば、非対話型タブに表示されるものもあります。CAE は、それらの要求のうち 1 つについてのみ true と表示され、対話型タブまたは非対話型タブに表示されます。管理者は、両方のタブをチェックして、ユーザーの認証が CAE 対応かどうかを確認する必要があります。 

### <a name="searching-for-specific-sign-in-attempts"></a>特定のサインイン試行の検索

検索を絞り込むにはフィルターを使用します。 たとえば、ユーザーが Teams にサインインした場合は、[アプリケーション] フィルターを使用して Teams に設定します。 管理者は、特定のサインインを見つけるために、対話型タブと非対話型タブの両方でサインインを調べることが必要な場合があります。 検索をさらに絞り込むには、複数のフィルターを適用できます。

## <a name="continuous-access-evaluation-workbooks"></a>継続的アクセス評価ブック

継続的アクセス評価分析情報ブックを使用すると、管理者はテナントの CAE 使用状況の分析情報を表示および監視できます。 テーブルには、IP の不一致を含む認証試行が表示されます。 このブックは、[条件付きアクセス] カテゴリの下のテンプレートとして見つかります。 

### <a name="accessing-the-cae-workbook-template"></a>CAE ブック テンプレートへのアクセス

ブックを表示する前に、Log Analytics の統合を完了する必要があります。 Azure AD サインイン ログを Log Analytics ワークスペースにストリーミングする方法の詳細については、「[Azure AD ログを Azure Monitor ログと統合する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)」を参照してください。
 
1.  **Azure portal** に、条件付きアクセス管理者、セキュリティ管理者、または全体管理者としてサインインします。 
1.  **[Azure Active Directory]**  >  **[ブック]** の順に移動します。
1.  **[Public Templates]\(パブリック テンプレート\)** で、 **[Continuous access evaluation insights]\(継続的アクセス評価の分析情報\)** を検索します。

[ ![ギャラリーで CAE 分析情報ブックを見つけて監視を続行します](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

**継続的アクセス評価分析情報** ブックには、以下のテーブルが含まれています。

### <a name="potential-ip-address-mismatch-between-azure-ad-and-resource-provider"></a>Azure AD とリソース プロバイダーの間で IP アドレスの不一致の可能性  

![IP アドレス不一致の可能性を示すブック テーブル 1](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

Azure AD とリソース プロバイダーの間で IP アドレスの不一致の可能性テーブルを使用すると、管理者は、Azure AD によって検出された IP アドレスがリソース プロバイダーによって検出された IP アドレスと一致しないセッションを調査できます。 

このブック テーブルでは、それぞれの IP アドレスと、セッションの間に CAE トークンが発行されたかどうかが表示されて、これらのシナリオが明らかになります。 

#### <a name="ip-address-configuration"></a>IP アドレスの構成

ID プロバイダーとリソース プロバイダーがそれぞれ異なる IP アドレスを認識する場合があります。 この不一致が発生する原因の例は次のとおりです。

- ネットワークで分割トンネリングが実装されています。
- リソース プロバイダーでは IPv6 アドレスが使用されていて、Azure AD では IPv4 アドレスが使用されています。
- ネットワークの構成により、Azure AD とリソース プロバイダーで認識されているクライアントの IP アドレスが異なります。

環境内にこのシナリオが存在する場合、無限ループを回避するために、Azure AD から 1 時間の CAE トークンが発行され、その 1 時間はクライアントの場所の変更が適用されません。 この場合でも、従来の 1 時間のトークンと比較してセキュリティは向上します。これは、クライアントの場所の変更イベント以外に他のイベントも評価されるためです。

管理者は、時間範囲とアプリケーションでフィルター処理されたレコードを表示できます。 管理者は、指定した期間中に検出された不一致の IPS の数と、サインインの総数を比較できます。 

管理者は、ユーザーのブロックを解除するために、信頼できる名前付きの場所に特定の IP アドレスを追加できます。

1.  **Azure portal** に、条件付きアクセス管理者、セキュリティ管理者、または全体管理者としてサインインします。 
1.  **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]**  >  **[ネームド ロケーション]** を参照します。 ここでは、信頼できる IP の場所を作成または更新できます。

> [!NOTE]
> 信頼できる名前付きの場所として IP アドレスを追加する前に、その IP アドレスが実際に目的の組織に属していることを確認します。

名前付きの場所の詳細については、[場所条件の使用](location-condition.md#named-locations)に関する記事を参照してください
 
## <a name="next-steps"></a>次のステップ

- [Azure AD ログを Azure Monitor ログと統合する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [場所条件の使用](location-condition.md#named-locations)
- [継続的アクセス評価](concept-continuous-access-evaluation.md)

---
title: Microsoft Sentinel でのマネージド セキュリティ サービス プロバイダー (MSSP) の知的財産権の保護 | Microsoft Docs
description: マネージド セキュリティ サービス プロバイダー (MSSP) が、Microsoft Sentinel で作成した知的財産を保護する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: a0e2874782432c707aabc571ed77370b512e2dbf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713119"
---
# <a name="protecting-mssp-intellectual-property-in-microsoft-sentinel"></a>Microsoft Sentinel で MSSP の知的財産権を保護する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、マネージド セキュリティ サービス プロバイダー (MSSP) が、Microsoft Sentinel で開発した知的財産 (Microsoft Sentinel の分析ルール、ハンティング クエリ、プレイブック、ブックなど) を保護するために使用できる方法について説明します。

どの方法を選択するかは、顧客がどのように Azure を購入するか、自分が[クラウド ソリューション プロバイダー (CSP)](#cloud-solutions-providers-csp) かどうか、または顧客が [Enterprise Agreement (EA) や従量課金制 (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) アカウントを持っているかどうかによって変わります。 以下のセクションでは、これらの方法を個別に説明します。

## <a name="cloud-solutions-providers-csp"></a>クラウド ソリューション プロバイダー (CSP)

Azure をクラウド ソリューション プロバイダー (CSP) として再販している場合は、顧客の Azure サブスクリプションを管理していることになります。 [Admin-On-Behalf-Of (AOBO)](/partner-center/azure-plan-manage) により、MSSP テナントの Admin Agents グループに属するユーザーには、顧客の Azure サブスクリプションに対する所有者アクセス権が付与されており、既定では顧客にはアクセス権がありません。

Admin Agents グループに属さない他の MSSP テナントのユーザーが顧客の環境にアクセスする必要がある場合は、[Azure Lighthouse](multiple-tenants-service-providers.md) の使用をお勧めします。 Azure Lighthouse を使用すると、組み込みのロールのいずれかを使用して、リソース グループやサブスクリプションなどの特定のスコープへのアクセス権をユーザーまたはグループに付与できます。

顧客ユーザーに Azure 環境へのアクセスを許可する必要がある場合は、サブスクリプション全体ではなく、*リソース グループ* レベルでアクセス権を付与し、必要に応じて環境の一部を表示または非表示にできるようにすることをお勧めします。

次に例を示します。

- アプリケーションが配置されている複数のリソース グループへのアクセス権を顧客に付与する場合、Microsoft Sentinel ワークスペースは顧客がアクセスできない別のリソース グループに保持します。

- この方法を使用すると、顧客は、自身のリソース グループに含まれる別個のリソースとして、選択したブックやプレイブックを表示できます。

リソース グループ レベルでアクセス権を付与しても、顧客は、アクセスが許可されているリソースのログ データ (VM のログなど) には引き続きアクセスできます。これは Microsoft Sentinel へのアクセス権がなくても可能です。 詳細については、「[リソースによる Microsoft Sentinel データへのアクセスを管理する](resource-context-rbac.md)」を参照してください。

> [!TIP]
> 顧客にサブスクリプション全体へのアクセスを提供する必要がある場合は、[Enterprise Agreement (EA)/従量課金制 (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) のガイダンスを参照してください。
>

### <a name="sample-microsoft-sentinel-csp-architecture"></a>Microsoft Sentinel CSP アーキテクチャの例

次の図は、CSP の顧客にアクセス権を提供する際に、[前のセクション](#cloud-solutions-providers-csp)で説明したアクセス許可がどのように機能するかを示しています。

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="CSP の顧客について Microsoft Sentinel の知的財産権を保護します。":::

この画像の内容:

- CSP サブスクリプションへの **所有者** アクセス権を付与されたユーザーは、MSSP Azure AD テナントで Admin Agents グループのユーザーになります。
- MSSP の他のグループは、Azure Lighthouse を介して顧客の環境にアクセスできます。
- 顧客による Azure リソースへのアクセスは、リソース グループ レベルで Azure RBAC によって管理されます。

    これにより、MSSP は必要に応じて Microsoft Sentinel コンポーネント (分析ルールやハンティング クエリなど) を非表示にすることができます。

詳細については、[Azure Lighthouse のドキュメント](../lighthouse/concepts/cloud-solution-provider.md)を参照してください。

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Enterprise Agreement (EA)/従量課金制 (PAYG)

Microsoft から直接購入した顧客は既に Azure 環境へのフル アクセスを持っているため、Azure サブスクリプションに含まれるものを顧客から見えないようにすることはできません。

代わりに、保護する必要があるリソースの種類に応じて、Microsoft Sentinel で開発した知的財産を次のように保護します。

### <a name="analytics-rules-and-hunting-queries"></a>分析ルールおよびハンティング クエリ

分析ルールとハンティング クエリはどちらも Microsoft Sentinel 内に含まれているため、Microsoft Sentinel ワークスペースから切り離すことはできません。

ユーザーは、Microsoft Sentinel 閲覧者のアクセス許可しか持っていなくてもクエリを表示できます。 この場合は、顧客のテナントではなく、独自の MSSP テナントで、分析ルールとハンティング クエリをホストすることをお勧めします。

これを行うには、Microsoft Sentinel が有効になっている独自のテナントにワークスペースが必要です。また、[Azure Lighthouse](multiple-tenants-service-providers.md) を使用して顧客のワークスペースを確認する必要があります。

MSSP テナントで、顧客テナントのデータを参照する分析ルールやハンティング クエリを作成するには、`workspace` ステートメントを次のように使用する必要があります。

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

分析ルールに `workspace` ステートメントを追加する場合は、次の点を考慮してください。

- **顧客のワークスペースではアラートが発生しません**。 この方法で作成されたルールについて、顧客のワークスペースでアラートやインシデントは作成されません。 アラートとインシデントはいずれも MSSP ワークスペースでのみ作成されます。

- **顧客ごとに個別のアラートを作成します**。 この方法を使用する場合は、顧客ごと、および検出ごとに個別のアラート ルールを使用することもお勧めします。各ケースで workspace ステートメントが異なるためです。

    アラート ルール名に顧客名を追加すると、アラートの発生源となっている顧客を簡単に識別できます。 個別にアラートを発行すると大量のルールが生成される可能性がありますが、これは、スクリプトまたは[コードとしての Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) を使用して管理できます。

    次に例を示します。

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="MSSP ワークスペースで顧客ごとに個別のルールを作成します。":::

- **顧客ごとに個別の MSSP ワークスペースを作成します**。 顧客ごと、および検出ごとに個別のルールを作成すると、ワークスペースでの分析ルールの数が最大数 (512) に達する可能性があります。 顧客数が多く、この上限に達することが予想される場合は、顧客ごとに個別の MSSP ワークスペースを作成することをお勧めします。

    次に例を示します。

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="MSSP テナントに顧客ごとのワークスペースとルールを作成します。":::

> [!IMPORTANT]
> この方法を正常に使用するポイントは、自動化を使用して、ワークスペース全体で大量のルールを管理することです。
>
> 詳細については、[クロスワークスペース分析ルール](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)に関する記事を参照してください。
>

### <a name="workbooks"></a>ブック

顧客によるコピーを禁止したい Microsoft Sentinel ブックを開発した場合は、独自の MSSP テナントでブックをホストします。 Azure Lighthouse を介して顧客のワークスペースにアクセスできることを確認してから、それらの顧客のワークスペースを使用するようにブックを変更してください。

次に例を示します。

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="クロスワークスペース ブック":::

詳細については、[クロスワークスペース ブック](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)に関する記事を参照してください。

コードのシークレットを保持したまま、顧客がブックの視覚エフェクトを表示できるようにするには、ブックを Power BI にエクスポートすることをお勧めします。

Power BI へのブックのエクスポート:

- **ブックの視覚エフェクトをより簡単に共有できます**。 顧客には Power BI ダッシュボードへのリンクを送信できます。このダッシュボードでは、Azure のアクセス許可なしで、報告されたデータを表示できます。
- **スケジュールを有効にします**。 特定の時点でのダッシュボードのスナップショットを含む電子メールを定期的に送信するように Power BI を構成します。

詳細については、「[Azure Monitor ログ データを Power BI にインポートする](../azure-monitor/logs/log-powerbi.md)」をご覧ください。

### <a name="playbooks"></a>プレイブック

プレイブックをトリガーする分析ルールが作成された場所に応じて、次のようにプレイブックを保護することができます。

- **MSSP ワークスペースで作成された分析ルール**。  MSSP テナントでプレイブックを作成し、すべてのインシデントとアラート データを MSSP ワークスペースから取得するようにしてください。 ワークスペースで新しいルールを作成するたびに、プレイブックをアタッチできます。

    次に例を示します。

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="MSSP ワークスペースで作成されたルール。":::

- **顧客のワークスペースで作成された分析ルール**。 Azure Lighthouse を使用して、顧客のワークスペースから、MSSP ワークスペースでホストされているプレイブックに分析ルールをアタッチできます。 この場合、プレイブックは、アラートとインシデント データに加え、その他の顧客情報を顧客のワークスペースから取得します。

    次に例を示します。

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="顧客のワークスペースで作成されたルール。":::

どちらの場合も、プレイブックで顧客の Azure 環境にアクセスする必要がある場合は、Lighthouse 経由でそのアクセス権を持つユーザー プリンシパルまたはサービス プリンシパルを使用します。

ただし、プレイブックで Azure AD、Office 365、Microsoft 365 Defender など、顧客のテナントにある Azure 以外のリソースにアクセスする必要がある場合は、適切なアクセス許可を持つサービス プリンシパルを顧客テナントで作成し、その ID をプレイブックに追加する必要があります。

> [!NOTE]
> プレイブックで自動化ルールを使用する場合は、プレイブックがあるリソース グループに対して自動化ルールのアクセス許可を設定する必要があります。
> 詳細については、「[プレイブックを実行するための自動化ルールへのアクセス許可](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks)」を参照してください。
>

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [MSSP 用 Microsoft Sentinel テクニカル プレイブック](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Microsoft Sentinel で複数のテナントを MSSP として管理する](multiple-tenants-service-providers.md)
- [ワークスペースおよびテナント全体での Microsoft Sentinel の拡張](extend-sentinel-across-workspaces-tenants.md)
- [ データの視覚化と監視](monitor-your-data.md)
- [チュートリアル: Microsoft Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)

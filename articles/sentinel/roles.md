---
title: Microsoft Azure Sentinel のアクセス許可 | Microsoft Docs
description: この記事では、Microsoft Azure Sentinel で Azure ロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdca3fe600fe41f0f4a5f54ebe718481881b1ae4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712188"
---
# <a name="permissions-in-microsoft-sentinel"></a>Microsoft Azure Sentinel のアクセス許可

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Azure Sentinel では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用して、Azure のユーザー、グループ、サービスに割り当てることができる[組み込みロール](../role-based-access-control/built-in-roles.md)が提供されています。

Azure RBAC を使用して、セキュリティ運用チーム内でロールを作成して割り当て、Microsoft Azure Sentinel への適切なアクセス権を付与します。 さまざまなロールを使用して、Microsoft Azure Sentinel のユーザーが表示および実行できることをきめ細かく制御できます。 Azure ロールは、Microsoft Azure Sentinel ワークスペースで直接割り当てるか (下記の注を参照してください)、またはワークスペースが属しているサブスクリプションまたはリソース グループで割り当てることができます (これは、Microsoft Azure Sentinel が継承するものです)。

## <a name="roles-for-working-in-microsoft-sentinel"></a>Microsoft Azure Sentinel で作業するためのロール

### <a name="microsoft-sentinel-specific-roles"></a>Microsoft Azure Sentinel 固有のロール

**Microsoft Azure Sentinel のすべての組み込みロールでは、Microsoft Azure Sentinel ワークスペース内のデータに対して読み取りアクセス権が付与されます。**

- [Microsoft Azure Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)では、データ、インシデント、ブック、その他の Microsoft Sentinel リソースを表示できます。

- [Microsoft Azure Sentinel レスポンダー](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)では、上記に加え、インシデントの管理 (割り当て、無視など) を行うことができます

- [Microsoft Azure Sentinel 共同作成者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)では、上記に加え、ブック、分析ルール、その他の Microsoft Sentinel リソースの作成と編集を行うことができます。

- [Microsoft Azure Sentinel Automation 共同作成者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)は、プレイブックに自動化ルールを追加することを Microsoft Azure Sentinel に許可します。 これはユーザー アカウント用ではありません。

> [!NOTE]
>
> - 最適な結果を得るには、これらのロールを、Microsoft Azure Sentinel ワークスペースが含まれる **リソース グループ** に割り当てる必要があります。 Microsoft Azure Sentinel をサポートするためにデプロイされるすべてのリソースを同じリソース グループに配置する必要があるため、このようにすると、それらのリソースにロールが適用されます。
>
> - もう 1 つの方法として、Microsoft Azure Sentinel **ワークスペース** 自体にロールを直接割り当てます。 これを行う場合は、そのワークスペースの SecurityInsights **ソリューション リソース** でも同じロールを割り当てる必要があります。 他のリソースでもそれらを割り当てることが必要になる場合があり、リソースに対するロールの割り当てを絶えず管理する必要があります。

### <a name="additional-roles-and-permissions"></a>追加のロールとアクセス許可

特定のジョブ要件があるユーザーには、タスクを実行するために、追加のロールまたは特定のアクセス許可を割り当てることが必要な場合があります。

- **プレイブックを使用して脅威への対応を自動化する**

    Microsoft Azure Sentinel では、自動化された脅威の対応に **プレイブック** を使用します。 プレイブックは、**Azure Logic Apps** に基づいて構築されている、別の Azure リソースです。 セキュリティ運用チームの特定のメンバーに、セキュリティ オーケストレーション、自動化、および対応 (SOAR) の操作に Logic Apps を使用する権能を割り当てることができます。 [Logic App 共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)ロールを使用して、プレイブックを使用するための明示的なアクセス許可を割り当てることができます。

- **Microsoft Azure Sentinel へのデータ ソースの接続**

    ユーザーが **データ コネクタ** を追加するには、Microsoft Azure Sentinel ワークスペースへの書き込みアクセス許可をそのユーザーに割り当てる必要があります。 また、関連するコネクタのページで示されている、各コネクタに必要な追加のアクセス許可を確認してください。

- **インシデントを割り当てるゲスト ユーザー**

    ゲスト ユーザーによるインシデント割り当てを可能にする必要がある場合、ユーザーには、Microsoft Azure Sentinel レスポンダー ロールに加えて、[ディレクトリ閲覧者](../active-directory/roles/permissions-reference.md#directory-readers)のロールも割り当てられている必要があります。 このロールは、Azure ロール *ではなく*、**Azure Active Directory** ロールであり、通常の (非ゲスト) ユーザーには既定でこのロールが割り当てられていることに注意してください。

- **ブックの作成と削除**

    ユーザーが Microsoft Azure Sentinel ブックを作成および削除するには、ユーザーには[監視共同作成者](../role-based-access-control/built-in-roles.md#monitoring-contributor)の Azure Monitor ロールも割り当てられている必要があります。 このロールは、作成と削除専用であり、ブックの "*使用*" には必要ありません。

### <a name="other-roles-you-might-see-assigned"></a>割り当てられる可能性のあるその他のロール

Microsoft Azure Sentinel 固有の Azure ロールの割り当てでは、Azure および Log Analytics の他の Azure ロールが他の目的でユーザーに割り当てられている可能性があります。 これらのロールでは、Microsoft Azure Sentinel ワークスペースや他のリソースへのアクセスを含む広範なアクセス許可のセットが付与されていることに注意してください。

- **Azure ロール:** [所有者](../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../role-based-access-control/built-in-roles.md#contributor)、および [閲覧者](../role-based-access-control/built-in-roles.md#reader)です。 Azure ロールによって、Log Analytics ワークスペースや Microsoft Azure Sentinel リソースなど、すべての Azure リソースへのアクセス権が付与されます。

- **Log Analytics のロール:** [Log Analytics 共同作成者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)、[Log Analytics 閲覧者](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics のロールでは、Log Analytics ワークスペースに対するアクセス権が付与されます。

たとえば、**Microsoft Azure Sentinel 閲覧者** ロールを割り当てられていて、**Microsoft Azure Sentinel 共同作成者** ロールを割り当てられていないユーザーでも、Azure レベルで **共同作成者** ロールが割り当てられている場合は、Microsoft Azure Sentinel の項目を編集できます。 そのため、Microsoft Azure Sentinel 内のユーザーにのみアクセス許可を付与する場合は、このユーザーの以前のアクセス許可を慎重に削除して、別のリソースに必要なアクセス権が無効にならないようにする必要があります。

## <a name="microsoft-sentinel-roles-and-allowed-actions"></a>Microsoft Azure Sentinel ロールと許可されているアクション

次の表は、Microsoft Azure Sentinel のロールと Microsoft Azure Sentinel で許可されるアクションをまとめたものです。

| Role | プレイブックを作成して実行する| 分析ルールとその他の Microsoft Azure Sentinel リソースを作成および編集する [*](#workbooks) | インシデントを管理する (無視、割り当てなど) | データ、インシデント、ブックなどの Microsoft Azure Sentinel リソースを表示する |
|---|---|---|---|---|
| Microsoft Sentinel 閲覧者 | -- | -- | -- | &#10003; |
| Microsoft Sentinel レスポンダー | -- | -- | &#10003; | &#10003; |
| Microsoft Sentinel 共同作成者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 共同作成者 + ロジック アプリの共同作成者 | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* ブックを作成および削除するには、追加の[監視共同作成者](../role-based-access-control/built-in-roles.md#monitoring-contributor)ロールが必要です。 詳細については、「[追加のロールとアクセス許可](#additional-roles-and-permissions)」を参照してください。
## <a name="custom-roles-and-advanced-azure-rbac"></a>カスタム ロールと高度な Azure RBAC

- **カスタム ロール**。 Azure 組み込みロールの使用に加え、またはその代わりに、Microsoft Azure Sentinel の Azure カスタム ロールを作成することができます。 Microsoft Azure Sentinel の Azure カスタム ロールは、[Microsoft Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) および [Azure Log Analytics リソース](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)に対する特定のアクセス許可に基づいて、他の[Azure カスタム ロール](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)を作成する場合と同じ方法で作成されます。

- **Log Analytics RBAC**。 Microsoft Azure Sentinel ワークスペースのデータに対して、Log Analytics の高度な Azure ロールベースのアクセス制御を使用できます。 これには、データ型ベースの Azure RBAC とリソースコンテキストの Azure RBAC の両方が含まれます。 詳細については、次を参照してください。

    - [Azure Monitor でログ データとワークスペースを管理する](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Microsoft Azure Sentinel のリソースコンテキストの RBAC](resource-context-rbac.md)
    - [テーブルレベルの RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    リソースコンテキスト RBAC とテーブルレベル RBAC は、Microsoft Azure Sentinel を丸ごと利用できる許可を与えることなく、Microsoft Azure Sentinel ワークスペースに含まれる特定のデータへのアクセスを許可する 2 つの手法です。

## <a name="role-recommendations"></a>ロールに関する推奨事項

Microsoft Azure Sentinel でロールとアクセス許可のしくみを理解したうえで、ユーザーにロールを適用するためのベスト プラクティスに関する次のガイダンスを参考にしてください。

|ユーザー タイプ  |職務 |リソース グループ  |説明  |
|---------|---------|---------|---------|
|**セキュリティ アナリスト**     | [Microsoft Sentinel レスポンダー](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)        | Microsoft Azure Sentinel のリソース グループ        | データ、インシデント、ブックなどの Microsoft Azure Sentinel リソースを表示する。 <br><br>インシデントの割り当てや解除など、インシデントを管理する。        |
|     | [Logic Apps の共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Microsoft Azure Sentinel のリソース グループ、またはプレイブックが格納されているリソース グループ        | プレイブックを分析ルールとオートメーション ルールにアタッチし、プレイブックを実行する。 <br><br>**注**: このロールにより、ユーザーがプレイブックを変更することもできます。         |
|**セキュリティ エンジニア**     | [Microsoft Sentinel 共同作成者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)       |Microsoft Azure Sentinel のリソース グループ         |   データ、インシデント、ブックなどの Microsoft Azure Sentinel リソースを表示する。 <br><br>インシデントの割り当てや解除など、インシデントを管理する。 <br><br>ブック、分析ルールなどの Microsoft Azure Sentinel リソースを作成および編集する。      |
|     | [Logic Apps の共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Microsoft Azure Sentinel のリソース グループ、またはプレイブックが格納されているリソース グループ        | プレイブックを分析ルールとオートメーション ルールにアタッチし、プレイブックを実行する。 <br><br>**注**: このロールにより、ユーザーがプレイブックを変更することもできます。         |
|  **サービス プリンシパル**   | [Microsoft Sentinel 共同作成者](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)      |  Microsoft Azure Sentinel のリソース グループ       | 管理タスクの自動化された構成 |
|     |         |        | |

> [!TIP]
> 取り込んでいる、または監視しているデータによっては、追加のロールが必要になることがあります。 たとえば、他の Microsoft ポータル内でサービスのデータ コネクタを設定するには、グローバル管理者ロールやセキュリティ管理者ロールなどの Azure AD ロールが必要になる場合があります。
>

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft Azure Sentinel ユーザーのロールと、各ロールでユーザーが実行できる操作について説明しました。

Azure のセキュリティとコンプライアンスに関するブログ記事は、[Microsoft Azure Sentinel ブログ](https://aka.ms/azuresentinelblog)で見つかります。

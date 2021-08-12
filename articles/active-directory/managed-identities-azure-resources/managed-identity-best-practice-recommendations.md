---
title: マネージド システム ID に関するベスト プラクティスの推奨事項
description: ユーザー割り当てとシステム割り当てのマネージド ID の使い分けに関する推奨事項
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/21/2021
ms.author: barclayn
ms.openlocfilehash: dec6cb642c5a5899354912f133decde45d631406
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953337"
---
# <a name="managed-identity-best-practice-recommendations"></a>マネージド ID のベスト プラクティスに関する推奨事項

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="choosing-system-or-user-assigned-managed-identities"></a>システムまたはユーザー割り当てのマネージド ID を選択する

ユーザー割り当てマネージド ID のほうが、システム割り当てマネージド ID より広範なシナリオにおいて効率的です。 ユーザー割り当てとシステム割り当てのいくつかのシナリオと推奨事項について、下の表を参照してください。

ユーザー割り当て ID は複数のリソースで使用できます。そのライフ サイクルは、関連しているリソースのライフ サイクルと切り離されています。 [どのリソースがマネージド ID をサポートしているかについてお読みください](./services-support-managed-identities.md)。

このライフ サイクルによって、リソースの作成と ID 管理の責任を分離することができます。 ユーザー割り当て ID とそのロールの割り当てを、それらを必要とするリソースより先に構成できます。 リソースを作成するユーザーが必要とするのは、ユーザー割り当て ID を割り当てるアクセス権だけです。新しい ID やロールの割り当てを作成する必要はありません。  

システム割り当て ID はリソースと共に作成および削除されるので、ロールの割り当てを前もって作成することはできません。 このシーケンスによって、リソースを作成しているユーザーがロールの割り当てを作成するアクセス権も持っていない場合、インフラストラクチャのデプロイ中にエラーが発生する可能性があります。

インフラストラクチャで複数のリソースが同じリソースへのアクセスを必要とする場合は、1 つのユーザー割り当て ID をそれらに割り当てることができます。 管理する ID とロールの割り当ての数が少なくなるため、管理のオーバーヘッドが削減されます。

各リソースがそれぞれの ID を持つことが必要な場合、あるいは一意のアクセス許可セットを必要とするリソースがあり、リソースが削除されたときに ID も削除したい場合は、システム割り当て ID を使用する必要があります。


| シナリオ| 推奨|Notes|
|---|---|---|
| マネージド ID を使用したリソースの迅速な作成 (エフェメラル コンピューティングなど) |  ユーザー割り当て ID | 複数の仮想マシンそれぞれに独自のシステム割り当て ID を指定してデプロイするなど、複数のマネージド ID を短い時間で作成しようとすると、Azure Active Directory オブジェクト作成のレート制限を超える場合があり、その要求は HTTP 429 エラーで失敗します。 <br/><br/>リソースを迅速に作成または削除している場合、システム割り当て ID を使用していると Azure Active Directory 内のリソース数の上限を超える可能性もあります。 削除されたシステム割り当て ID にはリソースからアクセスできなくなりますが、30 日後に完全に消去されるまでは制限数に対してカウントされます。<br/><br/>1 つのユーザー割り当て ID に関連付けられているリソースをデプロイするには、Azure Active Directory でサービス プリンシパルを 1 つだけ作成する必要があります。これにより、レート制限が回避されます。 事前に作成された 1 つの ID を使用することでも、それぞれ独自の ID を持つ複数のリソースを作成すると発生する可能性のあるレプリケーション遅延のリスクも減ります。<br/><br/>詳細については、[Azure サブスクリプション サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)に関するページを参照してください。 |
| レプリケートされたリソースおよびアプリケーション | ユーザー割り当て ID  |  同じタスクを実行するリソース (重複した Web サーバーや、アプリ サービスと仮想マシン上のアプリケーションで実行されている同一の機能など) には、通常、同じアクセス許可が必要です。 <br/><br/>同じユーザー割り当て ID を使用することで、必要なロールの割り当てが少なくなり、管理オーバーヘッドが減ります。 リソースは、種類が同じである必要はありません。 
|コンプライアンス| ユーザー割り当て ID  | 組織ですべての ID 作成が承認プロセスを通過しなければならない場合、1 つのユーザー割り当て ID を複数のリソースで使用すると、必要となる承認の数が、新しいリソースが作成されると作成されるシステム割り当て ID より少なくなります。 |
リソースがデプロイされる前に必要なアクセス権 |ユーザー割り当て ID| リソースによっては、デプロイの一環として特定の Azure リソースへのアクセスが必要になる場合があります。<br/><br/>この場合、システム割り当て ID が時間内に作成されない可能性があるため、既存のユーザー割り当て ID を使用する必要があります。|
監査ログ|システム割り当て ID|どのリソース (ID でない) がアクションを実行したのかをログ記録する必要がある場合は、システム割り当て ID を使用します。|
アクセス許可のライフサイクル管理|システム割り当て ID|リソースのアクセス許可がリソースと共に削除されるようにしたい場合は、システム割り当て ID を使用します。

### <a name="using-user-assigned-identities-to-reduce-administration"></a>ユーザー割り当て ID を使用した管理の削減

図は、システム割り当て ID とユーザー割り当て ID を、複数の仮想マシンが 2 つのストレージ アカウントにアクセスするために使用したときの違いを示しています。 

この図は、システム割り当て ID を持つ 4 つの仮想マシンを示しています。 それぞれの仮想マシンには、2 つのストレージ アカウントへのアクセスを許可する同じロールの割り当てがあります。

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities.png" alt-text="システム割り当て ID を使用してストレージ アカウントとキー コンテナーにアクセスする 4 つの仮想マシン。":::

ユーザー割り当て ID が 4 つの仮想マシンに関連付けられている場合、必要となるロールの割り当ては 2 つだけなのに対して、システム割り当て ID では 8 つが必要です。 仮想マシンの ID にさらなるロールの割り当てが必要な場合、それらはこの ID に関連付けられているすべてのリソースに付与されます。

:::image type="content" source="media/managed-identity-best-practice-recommendations/user-assigned-identities.png" alt-text="ユーザー割り当て ID を使用してストレージ アカウントとキー コンテナーにアクセスする 4 つの仮想マシン。":::

セキュリティ グループを使用して、必要となるロールの割り当ての数を減らすることもできます。 次の図では、システム割り当て ID を持つ 4 つの仮想マシンがセキュリティ グループに追加されており、ロールの割り当てはシステム割り当て ID でなく、グループに追加されています。 結果は似ていますが、この構成では、ユーザー割り当て ID と同じ Resource Manager テンプレート機能が提供されません。

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities-in-a-group.png" alt-text="4 つの仮想マシンのシステム割り当て ID が、ロール割り当てを持つセキュリティ グループに追加されている。":::

### <a name="multiple-managed-identities"></a>複数のマネージド ID

マネージド ID をサポートするリソースは、システム割り当て ID と、1 つ以上のユーザー割り当て ID の両方を使用できます。 

このモデルには、共有のユーザー割り当て ID を使用し、しかもきめ細かいアクセス許可を必要に応じて適用できる柔軟性があります。

下の例では、"仮想マシン 3" と "仮想マシン 4" は、認証時に使用するユーザー割り当て ID に応じて、両方のストレージ アカウントとキー コンテナーにアクセスできます。

:::image type="content" source="media/managed-identity-best-practice-recommendations/multiple-user-assigned-identities.png" alt-text="4 つの仮想マシン。2 つは複数のユーザー割り当て ID を持つ。":::

下の例では、"仮想マシン 4" にはユーザー割り当て ID とシステム割り当て ID の両方があり、認証時に使用する ID に応じて、両方のストレージ アカウントとキー コンテナーにアクセスできます。 システム割り当て ID のロールの割り当ては、その仮想マシンに固有です。

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-and-user-assigned-identities.png" alt-text="4 つの仮想マシン。1 つにはシステム割り当て ID とユーザー割り当て ID の両方がある。":::

## <a name="limits"></a>制限 

[マネージド ID](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits) の制限と、[カスタム ロールとロールの割り当て](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits)の制限をご覧ください。

## <a name="maintenance"></a>メンテナンス

システム割り当て ID は、リソースが削除されると自動的に削除されます。一方、ユーザー割り当て ID のライフサイクルは、それが関連付けられているリソースとは無関係です。

ユーザー割り当て ID が不要になった場合、リソースが関連付けられていない場合でも、手動で削除する必要があります。

ロールの割り当ては、システム割り当てまたはユーザー割り当てのマネージド ID のいずれかが削除されたときに、自動的には削除されません。 これらのロール割り当ては手動で削除して、サブスクリプションごとのロール割り当ての制限を超えないようにする必要があります。 

削除されたマネージド ID に関連付けられているロールの割り当ては、ポータルで表示したとき "ID が見つかりません" と表示されます。 詳細については、[こちら](../../role-based-access-control/troubleshooting.md#role-assignments-with-identity-not-found)を参照してください。

:::image type="content" source="media/managed-identity-best-practice-recommendations/identity-not-found.png" alt-text="ロールの割り当てでの「ID が見つかりません」。":::
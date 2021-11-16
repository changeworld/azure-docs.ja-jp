---
title: マネージド システム ID に関するベスト プラクティスの推奨事項
description: ユーザー割り当てとシステム割り当てのマネージド ID の使い分けに関する推奨事項
services: active-directory
documentationcenter: ''
author: barclayn
manager: karenh444
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/09/2021
ms.author: barclayn
ms.openlocfilehash: 400fe7d940a97ddf9fb885302edd93c8780491f5
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134808"
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

## <a name="follow-the-principle-of-least-privilege-when-granting-access"></a>アクセス権を付与する場合は最小特権の原則に従う

マネージド ID を含む任意の ID にサービスへのアクセス許可を付与する際は、常に目的のアクションを実行するために必要な最小限の権限を付与するようにします。 たとえば、あるマネージド ID を使用してストレージ アカウントからデータを読み取る場合、その ID にストレージ アカウントにデータを書き込むアクセス許可を付与する必要はありません。 追加のアクセス許可を付与する (たとえば、不必要であるに、も関わらず、マネージド ID を Azure サブスクリプションの共同作成者にするなど) と、その ID に関連するセキュリティの影響範囲が大きくなります。 その ID が侵害された場合の被害が最小限になるように、セキュリティの影響範囲は常に最小にする必要があります。

### <a name="consider-the-effect-of-assigning-managed-identities-to-azure-resources"></a>マネージド ID を Azure リソースに割り当てる影響を考慮する

Azure Logic App、Azure 関数、仮想マシンなどの Azure リソースにマネージド ID が割り当てられると、そのマネージド ID に付与されているすべてのアクセス許可が Azure リソースで利用できるようになるということに注意する必要があります。 もし、あるユーザーがこのリソースにコードをインストールまたは実行するアクセス権を持っている場合、そのユーザーはその Azure リソースに割り当てられている、または関連付けられているすべての ID へのアクセス許可を持っていることになるため、これは重要なことです。 マネージド ID の目的は、開発者がアクセスを得るために資格情報の処理を行ったり、これをコードに直接挿入したりすることなく、Azure リソース上で実行されるコードに他のリソースへのアクセス許可を与えることです。

例えば、マネージド ID (ClientId = 1234) に ***StorageAccount7755** _ への読み取りおよび書き込みアクセス許可が付与され、_*_LogicApp3388_*_ に割り当てられている場合、マネージド ID やストレージ アカウントに対する直接的な権限は持たないが、_*_LogicApp3388_*_ 内でコードを実行する権限を持つ Alice は、そのマネージド ID を使用するコードを実行することで、_ *_StorageAccount7755_** との間でデータの読み取りと書き込みを行うこともできます。

:::image type="content" source="media/managed-identity-best-practice-recommendations/security-considerations.png" alt-text="セキュリティのシナリオ":::

一般的に、コードを実行でき (Logic App など)、マネージド ID を持つリソースに対する管理者権限をユーザーに付与する際には、そのユーザーに割り当てられるロールを使ってリソース上でコードをインストールまたは実行できるかどうかを考慮し、そうである場合は必要な場合にのみ、そのロールを割り当てるようにします。


## <a name="maintenance"></a>メンテナンス

システム割り当て ID は、リソースが削除されると自動的に削除されます。一方、ユーザー割り当て ID のライフサイクルは、それが関連付けられているリソースとは無関係です。

ユーザー割り当て ID が不要になった場合、リソースが関連付けられていない場合でも、手動で削除する必要があります。

ロールの割り当ては、システム割り当てまたはユーザー割り当てのマネージド ID のいずれかが削除されたときに、自動的には削除されません。 これらのロール割り当ては手動で削除して、サブスクリプションごとのロール割り当ての制限を超えないようにする必要があります。 

削除されたマネージド ID に関連付けられているロールの割り当ては、ポータルで表示したとき "ID が見つかりません" と表示されます。 詳細については、[こちら](../../role-based-access-control/troubleshooting.md#role-assignments-with-identity-not-found)を参照してください。

:::image type="content" source="media/managed-identity-best-practice-recommendations/identity-not-found.png" alt-text="ロールの割り当てでの「ID が見つかりません」。":::

## <a name="limitation-of-using-azure-ad-groups-with-managed-identities-for-authorization"></a>マネージド ID を持つ Azure AD グループを認可に使用することの制限

サービスへのアクセスを許可するために Azure AD グループを使用するのは、認可プロセスを簡素化するための優れた方法です。 発想はシンプルです。アクセス許可をグループに付与し、ID をグループに ID を追加して、それらの ID が同じアクセス許可を継承するようにします。 これは、さまざまなオンプレミス システムで採用されている十分に確立されたパターンであり、ID がユーザーを表す場合は適切に機能します。 ただし、Azure AD のアプリケーションやマネージド ID など、人間以外の ID に関しては、今のところ、これとまったく同じメカニズムが十分に適しているとはいえません。 Azure AD や Azure ロールベースのアクセス制御 (Azure RBAC) を使用する現在の実装では、Azure AD によって発行されるアクセス トークンを各 ID の認証に使用します。 ただし、ID がグループに追加される場合、そのグループ メンバーシップは、Azure AD によって発行されるアクセス トークンではクレームとして表されます。 Azure RBAC ではこのクレームを使用して、アクセスを許可または拒否するための認可ルールをさらに評価します。  

グループ メンバーシップはアクセス トークン内のクレームであるため、グループ メンバーシップの変更はトークンが更新されるまで有効になりません。 人間のユーザーは、ログアウトして再度ログインすれば、新しいアクセス トークンを取得できます。 マネージド ID のトークンは、パフォーマンスと回復性を確保するために、基盤の Azure インフラストラクチャによってキャッシュされます。 これは、マネージド ID のグループ メンバーシップの変更が有効になるまでに数時間かかる場合があることを意味します。 現時点では、マネージド ID のトークンの有効期限が切れる前にトークンを強制的に更新することはできません。 したがって、マネージド ID を使用する Azure リソースに適切なアクセス権が付与されるまでの待ち時間は、ID で直接アクセス許可を追加または削除する場合は数分で済むのに対し、ID のグループ メンバーシップを変更してアクセス許可を追加または削除する場合には数時間かかることがあります。

アクセス許可を持つマネージド ID の追加または削除を Azure AD グループに対して行う代わりに、マネージド ID のアクセス許可の変更が速やかに有効になるよう、アクセス許可が ID に直接適用される[ユーザー割り当てマネージド ID](how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azcli) を使用して Azure リソースをグループ化することをお勧めします。 ユーザー割り当てマネージド ID は、1 つ以上の Azure リソースに割り当てて使用できるため、グループのように使用できます。 割り当て操作は、[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)および[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator)のロールを使用して制御できます。
---
title: Azure Active Directory のグループのライセンスに関する問題を解決する | Microsoft Docs
description: Azure Active Directory でグループベースのライセンスを使用している場合にライセンス割り当ての問題を特定して解決する方法
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d64cf71ea3a44b7539835e3616150218e8b3635
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861229"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Azure Active Directory のグループのライセンスに関する問題を特定して解決する

Azure Active Directory (Azure AD) のグループベースのライセンスでは、ユーザーのライセンス付与に関してエラー状態の概念が導入されています。 この記事では、ユーザーがこの状態になることがある理由を説明します。

グループベースのライセンスを使用せずにライセンスを個別のユーザーに直接割り当てると、割り当て処理が失敗することがあります。 たとえば、ユーザー システムに対して PowerShell コマンドレット `Set-MsolUserLicense` を実行した場合、このコマンドレットは、ビジネス ロジックに関連するたくさんの理由によってエラーになる可能性があります。 たとえば、ライセンス数の不足や、同時に割り当てることができない 2 つのサービス プランの競合などが発生することがあります。 問題は即座に報告されます。

グループベースのライセンスを使用している場合も同じエラーが発生する可能性がありますが、この場合のエラーは、Azure AD サービスがライセンスを割り当てているときにバック グラウンドで発生します。 このため、エラーを即座に伝達することはできません。 その代わりに、エラーはユーザー オブジェクトに記録され、管理ポータル経由で報告されます。 ユーザーへのライセンス付与という元の操作が無効になることはありませんが、後で調査して解決できるようにエラー状態にあることが記録されます。

## <a name="how-to-find-license-assignment-errors"></a>ライセンスの割り当てエラーを見つける方法
**ライセンスの割り当てエラーを見つけるには**

   1. 特定のグループ内でエラー状態にあるユーザーを見つけるには、そのグループのウィンドウを開きます。 エラー状態にあるユーザーが存在する場合は、**[ライセンス]** の下に通知が表示されます。

   ![グループ、エラー通知](./media/licensing-groups-resolve-problems/group-error-notification.png)

   2. その通知を選択すると、影響を受けているすべてのユーザーの一覧が開きます。 個別に各ユーザーを選択すると、詳細を表示できます。

   ![グループ、エラー状態のユーザーの一覧](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

   3. 少なくとも 1 つのエラーを含むすべてのグループを見つけるには、**[Azure Active Directory]** ブレードで、**[ライセンス]**、**[概要]** の順に選択します。 注意が必要なグループの場合は、情報ボックスが表示されます。

   ![概要、エラー状態にあるグループに関する情報](./media/licensing-groups-resolve-problems/group-errors-widget.png)

   4. エラーのあるグループの一覧を表示するには、ボックスをオンにします。 各グループを選択すると、詳細を表示できます。

   ![概要、エラーのあるグループの一覧](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


次のセクションでは、考えられる問題とその解決方法を個別に説明します。

## <a name="not-enough-licenses"></a>ライセンス数の不足

**問題:** グループに指定されたいずれかの製品について、利用できるライセンスの数が不足しています。 該当する製品のライセンスを追加で購入するか、他のユーザーかグループから未使用のライセンスを解放する必要があります。

利用できるライセンスの数を確認するには、**[Azure Active Directory]** > **[ライセンス]** > **[すべての製品]** を選択します。

ライセンスを使用中のユーザーとグループを確認するには、製品を選択します。 **[ライセンスされているユーザー]** には、ライセンスが直接または 1 つ以上のグループ経由で割り当てられているユーザーの一覧が表示されます。 **[ライセンスされているグループ]** には、その製品が割り当てられているグループがすべて表示されます。

**PowerShell:** PowerShell コマンドレットは、このエラーを _CountViolation_ として報告します。

## <a name="conflicting-service-plans"></a>サービス プランの競合

**問題:** グループに指定されたいずれかの製品に含まれているサービス プランが、他の製品でユーザーに既に割り当てられている別のサービス プランと競合しています。 一部のサービス プランは、関連する他のサービス プランと同じユーザーに割り当てられないように構成されています。

各データ メンバー フィールドが JSON オブジェクトにマップされ、フィールド名がオブジェクトの "key" 部分にマップされ、"value" 部分がオブジェクトの値の部分に再帰的にマップされます。 すべてのプランを有効にした Office 365 Enterprise *E1* のライセンスがユーザーに直接割り当てられているとします。 このユーザーが、Office 365 Enterprise *E3* 製品を割り当てられたグループに追加された場合、 この E3 製品には E1 に含まれているプランと重複できないサービス プランが含まれているため、グループのライセンス割り当ては、"競合するサービス プラン" エラーで失敗します。 この例では、競合しているサービス プランは次のとおりです。

-   SharePoint Online (プラン 2) と SharePoint Online (プラン 1) の競合
-   Exchange Online (プラン 2) と Exchange Online (プラン 1) の競合

この競合を解決するには、2 つのプランを無効にする必要があります。 ユーザーに直接割り当てられている E1 ライセンスを無効にできます。 または、グループのライセンス割り当てを変更して、E3 ライセンスのプランを無効にする必要があります。 E3 ライセンスのコンテキストで冗長である場合は、E1 ライセンスをユーザーから削除することもできます。

製品ライセンスの競合を解決する方法の決定は、常に管理者に委ねられます。 ライセンスの競合が Azure AD によって自動的に解決されることはありません。

**PowerShell:** PowerShell コマンドレットは、このエラーを _MutuallyExclusiveViolation_ として報告します。

## <a name="other-products-depend-on-this-license"></a>特定のライセンスに依存する他の製品

**問題:** グループに指定されたいずれかの製品に、他の製品の他のサービス プランが機能するうえで有効である必要があるサービス プランが含まれています。 このエラーは、Azure AD がサービス プランを削除しようとしたときに発生します。 たとえば、ユーザーをグループから削除したときに、発生する可能性があります。

この問題を解決するには、他の方法を使用して目的のプランがユーザーに引き続き割り当てられていること、または依存するサービスがこれらのユーザーに対して無効になっていることを確認する必要があります。 その後、ユーザーからグループ ライセンスを問題なく削除できます。

**PowerShell:** PowerShell コマンドレットは、このエラーを _DependencyViolation_ として報告します。

## <a name="usage-location-isnt-allowed"></a>利用場所が許可されていない

**問題:** 一部の Microsoft サービスは、地域の法律と規制が理由ですべての場所で利用することはできません。 ライセンスをユーザーに割り当てる前に、ユーザーの **[利用場所]** プロパティを指定する必要があります。 場所は、Azure Portal の **[ユーザー]**  >  **[プロファイル]**  >  **[設定]** セクションで指定できます。

利用場所がサポートされていないユーザーへのグループ ライセンスの割り当てが Azure AD により試行されると、その操作は失敗し、エラーがユーザーに記録されます。

この問題を解決するには、利用場所がサポートされていないユーザーをライセンス グループから削除します。 または、現在の利用場所の値が実際のユーザーの場所を表していない場合は、ライセンスが次回正しく割り当てられるように値を変更できます (新しい場所がサポートされている場合)。

**PowerShell:** PowerShell コマンドレットは、このエラーを _ProhibitedInUsageLocationViolation_ として報告します。

> [!NOTE]
> Azure AD によってグループ ライセンスが割り当てられるとき、指定された利用場所がないユーザーは、ディレクトリの場所を継承します。 管理者は、地域の法律と規制を遵守するために、グループベースのライセンスを使用する前に、ユーザーに対して正しい利用場所の値を設定することをお勧めします。

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>1 つのグループに複数の製品ライセンスがある場合

1 つのグループに複数の製品ライセンスを割り当てることができます。 たとえば、Office 365 Enterprise E3 と Enterprise Mobility + Security をグループに割り当てて、ユーザーによる製品内の全サービスの使用を簡単に有効にできます。

グループに指定したすべてのライセンスは、Azure AD によって各ユーザーに割り当てられます。 Azure AD が、ビジネス ロジックの問題が原因でいずれかの製品を割り当てることができない場合は、グループの他のライセンスも割り当てられません。 たとえば、ライセンス数が不足している場合や、ユーザーで有効になっている他のサービスと競合する場合などです。

割り当てられなかったユーザーと、どの製品がその問題の影響を受けているかを確認できます。

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>前提条件のある製品のライセンスを管理する方法

Microsoft Online 製品の中には "*アドオン*" であるものがあります。 ユーザーやグループにライセンスを割り当てるには、前提条件のサービス プランをユーザーまたはグループに対して有効にする必要があります。 グループごとにライセンスを付与する場合は、前提条件とアドオンのサービス プランが同じグループに存在する必要があります。 この措置によって、そのグループに追加されたユーザーに、完全に動作する製品が与えられます。 次の例について考察してみましょう。

Microsoft Workplace Analytics はアドオン製品です。 同じ名前の単一サービス プランが含まれています。 このサービス プランは、次の前提条件のいずれかが割り当てられている場合にのみ、ユーザーまたはグループに割り当てることができます。
- Exchange Online (プラン 1) 
- Exchange Online (プラン 2)

この製品だけをグループに割り当てようとすると、ポータルからエラーが返されます。 エラー通知をクリックすると、次の詳細が表示されます。

![グループ、前提条件がない](./media/licensing-groups-resolve-problems/group-prerequisite-required.png)

詳細を選択すると、次のエラー メッセージが表示されます。

>ライセンス操作に失敗しました。 Make sure that the group has necessary services before adding or removing a dependent service. **サービス Microsoft Workplace Analytics では、Exchange Online (プラン 2) も有効にする必要があります。**

このアドオン ライセンスをグループに割り当てるには、そのグループに前提条件のサービス プランも含まれている必要があります。 そこで、たとえば、完全版 Office 365 E3 製品が既に含まれている既存のグループを更新してから、アドオン製品を追加します。

必要最小限の製品だけを含むスタンドアロン グループを作成することで、アドオンが機能する場合もあります。 この方法は、選択されたユーザーに、アドオン製品のライセンスを付与するときにのみご利用いただけます。 この例では、次の製品を同じグループに割り当てました。
- Office 365 Enterprise E3 (Exchange Online (プラン 2) サービス プランのみが有効)
- Microsoft Workplace Analytics

![グループ、前提条件がある](./media/licensing-groups-resolve-problems/group-addon-with-prerequisite.png)

今後は、このグループに追加されたユーザーすべてが、E3 製品のライセンスと Workplace Analytics 製品のライセンスを 1 つずつ使用できます。 また、そのユーザーは、完全版 E3 製品を提供する別のグループに属することもできますが、使用できるその製品のライセンスは引き続き 1 つだけです。

> [!TIP]
> 前提条件のサービス プランごとに複数のグループを作成できます。 たとえば、Office 365 Enterprise E1 と Office 365 Enterprise E3 の両方をユーザーに使用する場合、2 つのグループを作成して Microsoft Workplace Analytics にライセンスを付与できます。1 つは前提条件として E1 を利用するグループ、もう 1 つは E3 を利用するグループです。 これにより、追加のライセンスを使用せずに、E1 ユーザーと E3 ユーザーにアドオンを配布できます。

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>Exchange Online でプロキシ アドレスの重複があるために、ユーザーのライセンス割り当てがエラーを記録することなく失敗する

Exchange Online を使用する場合は、テナント内の一部のユーザーが、同じプロキシ アドレスの値で間違って構成されている可能性があります。 グループ ベースのライセンスがこのようなユーザーにライセンス割り当てを試みると、エラーが発生しますが、そのエラーは記録されません。 エラーが記録されないのは、プレビュー版ではこの機能が制限されているからです。この問題は、"*一般公開*" が開始されるまでには解決される予定です。

> [!TIP]
> 一部のユーザーがライセンスを得られず、そのユーザーに関してエラーが記録されていない場合は、まず、ユーザーに重複するプロキシ アドレスがないかどうかを確認してください。
> 重複したプロキシ アドレスの有無を確認するには、次の PowerShell コマンドレットを Exchange Online に対して実行します。
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> この問題の詳細については、「[Exchange Online のエラー メッセージ: "プロキシ アドレス <アドレス> は既に使用されています"](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online)」を参照してください。 この記事では、[リモート PowerShell を使用して Exchange Online に接続する方法](https://technet.microsoft.com/library/jj984289.aspx)に関する情報も確認できます。

関連するユーザーのプロキシ アドレスの問題を解決したら、グループのライセンス処理を強制的に実行して、ライセンスが適用できるようになったことを確認してください。

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>グループでライセンスの処理を強制してエラーを解決する方法

エラーを解決した手順によっては、グループの処理を手動でトリガーして、ユーザーの状態を更新しなければならない場合があります。

たとえば、直接的に割り当てられたライセンスをユーザーから削除することで、一部のライセンスを解放した場合は、前に失敗したグループの処理を手動でトリガーして、すべてのユーザー メンバーに対して、完全にライセンスを付与する必要があります。 グループを処理するには、グループ ウィンドウに移動して **[ライセンス]** を開き、ツール バーの **[再処理]** ボタンを選択します。

## <a name="next-steps"></a>次の手順

グループによるライセンス管理の他のシナリオについては、以下を参照してください。

* [Assigning licenses to a group in Azure Active Directory](licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [What is group-based licensing in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](licensing-groups-migrate-users.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](licensing-group-advanced.md)

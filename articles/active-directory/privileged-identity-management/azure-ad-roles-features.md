---
title: Privileged Identity Management (PIM) で Azure AD ロールを管理する | Microsoft Docs
description: Privileged Identity Management (PIM) での割り当てのために、Azure AD ロールを管理する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225471"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management での Azure AD ロールの管理機能

Privileged Identity Management での Azure AD ロールの管理機能が更新され、Azure AD ロールと Azure リソース ロールの管理方法が統合されました。 以前は、Azure リソース ロール向けの Privileged Identity Management には Azure AD ロールでは利用できないいくつかの重要な機能がありました。

最近ロール アウトされた更新では、これらの 2 つを単一の管理エクスペリエンスに統合し、Azure AD ロールでも Azure リソース ロールと同じ機能を利用できるようになりました。 この記事では、更新された機能と要件を説明します。


## <a name="time-bound-assignments"></a>期限付き割り当て

以前の Azure AD ロール向け Privileged Identity Management では、ロールの割り当てには 2 つの状態がありました。 *[eligible]\(有資格\)* および *[permanent]\(永続的)* です。 それぞれのタイプの割り当てに、開始時刻と終了時刻を設定できるようになりました。 この追加により、割り当てに次の 4 つの状態を設定することができます。

- [Eligible permanently]\(永続的に有資格\)
- [Active permanently]\(永続的にアクティブ\)
- [Eligible]\(有資格\)。これは、割り当てに開始日と終了日が指定されています
- [Active]\(アクティブ\)。これは、割り当てに開始日と終了日が指定されています

ユーザーに有資格の割り当てを付与して毎回ロールをアクティブにするのが望ましくないケースでは、多くの場合、割り当てに有効期限を設定することで Azure AD 組織を保護することができます。 たとえば、有資格である一時的なユーザーが何人かいる場合、有効期限を設定して、それらのユーザーの作業が終了したらロールの割り当てから自動的に削除するようにすることができます。

## <a name="new-role-settings"></a>新しいロール設定

Azure AD ロール向けの新しい設定も追加されました。 以前は、アクティブ化はロールごとにのみ構成することができました。 つまり、多要素認証要件やインシデントまたは要求チケット要件などのアクティブ化設定は、特定のロールに対して有資格であるすべてのユーザーに適用されました。 今では、ロールをアクティブ化する前に個々のユーザーが多要素認証を実行する必要があるかどうかを構成できるようになりました。 さらに、特定のロールに関連する Privileged Identity Management のメールを詳細に管理できるようになりました。

## <a name="extend-and-renew-assignments"></a>割り当ての延長と更新

期限付き割り当てについて知ると、期限が切れたらロールはどうなるのか、という疑問がわくと思います。 この新しいバージョンでは、このシナリオに対して 2 つの選択肢があります。

- 延長 - ロールの割り当ての期限が近付くと、ユーザーは Privileged Identity Management を使用してそのロールの割り当ての延長を要求できます
- 更新 - ロールの割り当ての期限が切れた場合、ユーザーは Privileged Identity Management を使用してそのロールの割り当ての更新を要求できます

ユーザーが行うこれらの操作には、グローバル管理者または特権ロール管理者からの承認が必要です。 管理者は、これらの有効期限を管理する仕事から解放されます。 延長または更新の要求を待ち、その要求が有効であれば承認するだけでよいのです。

## <a name="api-changes"></a>API の変更

顧客が更新されたバージョンを自分の Azure AD 組織にロール アウトした場合、既存の Graph API は使用できなくなります。 [Azure リソース ロール向け Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta) の使用に移行する必要があります。 その API を使用して Azure AD ロールを管理するには、署名内の `/azureResources` を `/aadroles` と置き換え、`resourceId` にディレクトリ ID を使用します。

Microsoft は、以前の API を使用しているすべての顧客に連絡し、この変更について事前に伝えるためにできる限りのことをしてきました。 自分の Azure AD 組織が新しいバージョンに移行したのに、まだ古い API を使用している場合は、pim_preview@microsoft.com から Microsoft のチームに連絡してください。

## <a name="powershell-change"></a>PowerShell の変更

Azure AD ロール向けの Privileged Identity Management PowerShell モジュールを使用している顧客の場合、更新により、PowerShell は使用できなくなります。 以前のコマンドレットの代わりに、Azure AD プレビュー PowerShell モジュール内の Privileged Identity Management コマンドレットを使用する必要があります。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)から Azure AD PowerShell モジュールをインストールします。 これで、[この PowerShell モジュールの PIM 操作のドキュメントとサンプルを確認](powershell-for-azure-ad-roles.md)できます。

## <a name="next-steps"></a>次のステップ

- [Azure AD のカスタム ロールを割り当てる](azure-ad-custom-roles-assign.md)
- [Azure AD のカスタム ロールの割り当てを削除または更新する](azure-ad-custom-roles-update-remove.md)
- [Azure AD のカスタム ロールの割り当てを構成する](azure-ad-custom-roles-configure.md)
- [Azure AD のロールの定義](../users-groups-roles/directory-assign-admin-roles.md)

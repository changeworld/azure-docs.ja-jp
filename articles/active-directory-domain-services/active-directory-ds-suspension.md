---
title: 'Azure Active Directory Domain Services: 中断されたドメイン | Microsoft Docs'
description: マネージド ドメインの一時停止と削除
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 99896b0f618151cd368a21c4a212b7d8e28b7a6f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126095"
---
# <a name="suspended-domains"></a>中断されたドメイン
Azure AD Domain Services が長期間にわたりマネージド ドメインにサービスを提供できない場合、マネージド ドメインは中断済み状態になります。 この記事では、マネージド ドメインが中断される理由、および中断されたドメインを修復する方法について説明します。


## <a name="states-your-managed-domain-can-be-in"></a>マネージド ドメインがなる可能性のある状態

![中断されたドメインのタイムライン](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

上の図は、Azure AD Domain Services のマネージド ドメインがなる可能性のある状態を示したものです。

### <a name="running-state"></a>"実行中" 状態
正しく構成され、定期的に動作しているマネージド ドメインは、**実行中**状態になります。

**予想されること:**
* Microsoft は、マネージド ドメインの正常性を定期的に監視できます。
* マネージド ドメインのドメイン コントローラーには、修正プログラムと更新プログラムが定期的に適用されます。
* Azure Active Directory からの変更が、マネージド ドメインに定期的に同期されます。
* マネージド ドメインのバックアップが定期的に取得されます。


### <a name="needs-attention-state"></a>"要注意" 状態
管理者による対応が必要な問題が発生した場合、マネージド ドメインは**要注意**状態になります。 マネージド ドメインの正常性ページには、この状態でのアラートが表示されます。 たとえば、仮想ネットワークに対して制限の厳しい NSG が構成された場合、Microsoft はマネージド ドメインを更新および監視できないことがあります。 この無効な構成の結果としてアラートが生成され、マネージド ドメインは "要注意" 状態に置かれます。

各アラートには解決手順があります。 一部のアラートは一時的なものであり、サービスによって自動的に解決されます。 他のアラートの一部は、そのアラートに対応する解決手順に従って解決できます。 一部の重要なアラートを解決するには、Microsoft のサポートに問い合わせる必要があります。

詳細については、[マネージド ドメインでのアラートのトラブルシューティング方法](active-directory-ds-troubleshoot-alerts.md)に関するページを参照してください。

**予想されること:**

場合によっては (たとえば、無効なネットワーク構成がある場合)、マネージド ドメインのドメイン コントローラーに到達できない可能性があります。 この状態では、Microsoft はマネージド ドメインの監視、パッチ適用、更新、バックアップが定期的に行われることを保証できません。

* アラートが解決されるまで、マネージド ドメインは異常な状態になり、継続的な正常性の監視が停止する場合があります。
* マネージド ドメインのドメイン コントローラーに、修正プログラムや更新プログラムが適用されない可能性があります。
* Azure Active Directory からの変更が、マネージド ドメインに同期されないことがあります。
* マネージド ドメインのバックアップは、可能な場合は行われることがあります。
* マネージド ドメインに影響を与えているアラートを解決すると、マネージド ドメインを "実行中" 状態に復元できる場合があります。
* Microsoft がドメイン コントローラーに到達できない構成の問題に対しては、重大アラートがトリガーされます。 このようなアラートが 15 日以内に解決されない場合、マネージド ドメインは "中断済み" 状態に置かれます。


### <a name="suspended-state"></a>"中断済み" 状態
マネージド ドメインは、次のような理由で**中断済み**状態になります。
* 1 つ以上の重大アラートが、15 日以内に解決されなかった場合。 重大アラートは、Azure AD Domain Services で必要なリソースへのアクセスを妨げる誤った構成によって発生する可能性があります。
    * たとえば、マネージド ドメインで [AADDS104: ネットワーク エラー](active-directory-ds-troubleshoot-nsg.md) アラートが 15 日より長く解決されない場合。
* Azure サブスクリプションに請求の問題がある場合、または Azure サブスクリプションの有効期限が切れた場合。

Microsoft がドメインの管理、監視、パッチ適用、バックアップを継続的に行うことができない場合、マネージド ドメインは中断されます。

**予想されること:**
* マネージド ドメインのドメイン コントローラーはプロビジョニング解除され、仮想ネットワーク内で到達できなくなります。
* インターネット経由でのマネージド ドメインへの Secure LDAP アクセスは (有効になっている場合)、動作を停止します。
* マネージド ドメインに対する認証、ドメイン参加済み仮想マシンへのログオン、および LDAP/LDAPS 経由での接続の際に、問題を認識します。
* マネージド ドメインのバックアップは行われなくなります。
* Azure AD との同期は停止します。
* マネージド ドメインが "中断済み" 状態になった原因のアラートを解決した後、サポートに連絡してください。
* サポートは、作成されてから 30 日未満のバックアップが存在する場合にのみ、マネージド ドメインを復元できます。

マネージド ドメインが中断済み状態になっているのは 15 日間のみです。 マネージド ドメインを復旧するには、重大アラートをすぐに解決することをお勧めします。


### <a name="deleted-state"></a>"削除済み" 状態
"中断済み" 状態で 15 日間放置されたマネージド ドメインは、**削除**されます。

**予想されること:**
* マネージド ドメインのすべてのリソースとバックアップが削除されます。
* マネージド ドメインを復元することはできず、Azure AD Domain Services を使うには新しいマネージド ドメインを作成する必要があります。
* 削除されたマネージド ドメインに対しては課金されません。


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>マネージド ドメインが中断されているかどうかを確認する方法
Azure portal の Azure AD Domain Services 正常性ページに、ドメインが中断されることを宣言する[アラート](active-directory-ds-troubleshoot-alerts.md)が表示されます。 ドメインの状態も "中断済み" と表示されます。


## <a name="restore-a-suspended-domain"></a>中断されているドメインを復元する
"中断済み" 状態のドメインを復元するには、次の手順のようにします。

1. Azure portal で、[Azure AD Domain Services ページ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
2. マネージド ドメインをクリックします。
3. 左側のナビゲーションで、**[正常性]** をクリックします。
4. アラートをクリックします。 アラート ID は、中断の原因によって AADDS503 または AADDS504 のいずれかになります。
5. アラートで提供される解決リンクをクリックし、アラートを解決する手順に従います。

マネージド ドメインを復元できるのは、最後のバックアップの日付までだけです。 最後のバックアップの日付は、マネージド ドメインの [正常性] ページに表示されます。 最後のバックアップより後に発生した変更は復元できません。 マネージド ドメインのバックアップは、最大 30 日間保存されます。 30 日より古いバックアップは削除されます。


## <a name="next-steps"></a>次の手順
- 
  [マネージド ドメインのアラートを解決する](active-directory-ds-troubleshoot-alerts.md)
- [Azure AD Domain Services の詳細を確認する](active-directory-ds-overview.md)
- [製品チームに連絡する](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。

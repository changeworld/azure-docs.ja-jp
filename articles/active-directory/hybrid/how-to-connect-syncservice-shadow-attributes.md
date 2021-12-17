---
title: Azure AD Connect 同期サービスのシャドウ属性 | Microsoft Docs
description: Azure AD Connect 同期サービスのシャドウ属性の機能について説明します。
services: active-directory
author: billmath
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/29/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2274099803961fb477f0929c801e2fc341dd4b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355265"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect 同期サービスのシャドウ属性
Azure AD のほとんどの属性は、オンプレミスの Active Directory の場合と同じように表現されます。 ただし、一部の属性には特別な処理が必要であるため、Azure AD の属性値が Azure AD Connect で同期された値と異なる場合があります。

## <a name="introducing-shadow-attributes"></a>シャドウ属性の概要
Azure AD の一部の属性には、2 つの表現があります。 オンプレミスの値と計算された値の両方が格納されます。 これらの特別な属性を、シャドウ属性と呼びます。 この動作が見られる最も一般的な 2 つの属性に、**userPrincipalName** と **proxyAddress** があります。 属性値に変化が起こるのは、これらの属性に未確認のドメインを表す値が存在する場合です。 ただし、Connect の同期エンジンがその観点からシャドウ属性の値を読み取るため、この属性は Azure AD によって確認されています。

Azure Portal または PowerShell を使用してシャドウ属性を確認することはできません。 ただし、この概念を理解することは、属性の値がオンプレミスとクラウドで異なるという特定のシナリオのトラブルシューティングに役立ちます。

この動作を理解するには、次の Fabrikam の例をご覧ください。  
![対応する Azure AD ドメインの値が "Not Added"、"Not Verified"、および "Verified" である、Active Directory UPN サフィックスのいくつかの例を示すスクリーンショット。](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
オンプレミスの Active Directory 内に複数の UPN サフィックスがありますが、確認済みなのは 1 つのみです。

### <a name="userprincipalname"></a>userPrincipalName
ユーザーの未確認ドメイン内には、次の属性値があります。

| Attribute | 値 |
| --- | --- |
| オンプレミスの userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD の shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD の userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

userPrincipalName 属性は、PowerShell を使用しているときに表示される値です。

実際のオンプレミスの属性値は Azure AD に格納されるため、ユーザーが fabrikam.com ドメインを確認するときに、Azure AD は shadowUserPrincipalName の値で userPrincipalName 属性を更新します。 これらの値を更新するために Azure AD Connect の変更を同期する必要はありません。

### <a name="proxyaddresses"></a>proxyAddresses
確認済みドメインのみを含めるための同じプロセスは proxyAddresses でも発生しますが、いくつかの追加のロジックが使用されます。 確認済みドメインのチェックは、メールボックスのユーザーに対してのみ発生します。 メールが有効なユーザーまたは連絡先は別の Exchange 組織内のユーザーであることを意味するため、proxyAddresses のあらゆる値をこれらのオブジェクトに追加することができます。

オンプレミスまたは Exchange Online のメールボックスのユーザーの場合、確認済みドメインの値のみが表示されます。 次のようになります。

| Attribute | 値 |
| --- | --- |
| オンプレミスの proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online の proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

この場合、**smtp:abbie.spencer\@fabrikam.com** は、ドメインが確認されなかったため、削除されました。 ただし、Exchange では **SIP:abbie.spencer\@fabrikamonline.com** の追加も行われています。 Fabrikam はオンプレミスで Lync/Skype を使用していませんが、Azure AD と Exchange Online ではその準備が行われます。

proxyAddresses のこのロジックは、**ProxyCalc** と呼ばれます。 ProxyCalc は、ユーザーに対する以下の変更のたびに呼び出されます。

- ユーザーが、Exchange のライセンスを与えられていなかったとしても、Exchange Online を含むサービス プランを割り当てられたとき。 たとえば、SharePoint Online しか割り当てられていなかったユーザーに Office E3 SKU が割り当てられた場合です。 この条件は、メールボックスがまだオンプレミスの場合でも当てはまります。
- 属性 msExchRecipientTypeDetails が値を持つとき。
- proxyAddresses または userPrincipalName に変更を加えるとき。

ShadowProxyAddresses に未確認ドメインが含まれ、クラウド ユーザーに次のプロパティのいずれかが構成されている場合、ProxyCalc ではアドレスをサニタイズします。 
- ユーザーが EXO サービスの種類のプランが有効な状態でライセンスされている (MyAnalytics を除く)  
- ユーザーに MSExchRemoteRecipientType が設定されている (null 以外)  
- ユーザーが共有リソースと見なされている

共有リソースと見なされるために、クラウド ユーザーでは CloudMSExchRecipientDisplayType に次の値のいずれかが設定されます。 

 |オブジェクトの表示の種類|値 (10 進数)|
 |-----|-----|
 |MailboxUser|  0|
 |DistributionGroup|    1|
 |PublicFolder| 2|
 |DynamicDistributionGroup| 3|
 |Organization| 4|
 |PrivateDistributionList|  5|
 |RemoteMailUser|   6|
 |ConferenceRoomMailbox|    7|
 |EquipmentMailbox| 8|
 |ArbitrationMailbox|   10|
 |MailboxPlan|  11|
 |LinkedUser|   12|
 |RoomList| 15|
 |SyncedMailboxUser|    -2147483642|
 |SyncedUDGasUDG|   -2147483391|
 |SyncedUDGasContact|   -2147483386|
 |SyncedPublicFolder|   -2147483130|
 |SyncedDynamicDistributionGroup|   -2147482874|
 |SyncedRemoteMailUser| -2147482106|
 |SyncedConferenceRoomMailbox|  -2147481850|
 |SyncedEquipmentMailbox|   -2147481594|
 |SyncedUSGasUDG|   -2147481343|
 |SyncedUSGasContact|   -2147481338|
 |ACLableSyncedMailboxUser| -1073741818|
 |ACLableSyncedRemoteMailUser|  -1073740282|
 |ACLableSyncedUSGasContact|    -1073739514|
 |SyncedUSGasUSG|   -1073739511|
 |SecurityDistributionGroup|    1043741833|
 |SyncedUSGasUSG|   1073739511|
 |ACLableSyncedUSGasContact|    1073739514|
 |RBAC ロール グループ|  1073741824|
 |ACLableMailboxUser|   1073741824|
 |ACLableRemoteMailUser|    1073741830|


>[!NOTE]
> CloudMSExchRecipientDisplayType は Azure AD 側には表示されず、Exchange Online コマンドレット [Get-Recipient](/powershell/module/exchange/get-recipient) などを使用してしか表示できません。  
>
>例:
> ```PowerShell
>   Get-Recipient admin | fl *type*
> ```
>

ProxyCalc は、ユーザーの変更処理に時間がかかる場合があるため、Azure AD Connect のエクスポート プロセスと同期されません。

> [!NOTE]
> この ProxyCalc のロジックは、高度なシナリオでその他の動作を行いますが、このトピックには記載していません。 このトピックは動作の説明を目的とするため、文書化されているのは一部の内部ロジックのみです。

### <a name="quarantined-attribute-values"></a>検疫済みの属性値
シャドウ属性は、属性値が重複している場合にも使用されます。 詳細については、[重複属性の回復性](how-to-connect-syncservice-duplicate-attribute-resiliency.md)に関するページを参照してください。

## <a name="see-also"></a>参照
* [Azure AD Connect Sync](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)

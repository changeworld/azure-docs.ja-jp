---
title: ユーザーベースのサービス アカウントをセキュリティで保護する | Azure Active Directory
description: ユーザーベースのサービス アカウントをセキュリティで保護するためのガイド
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dc3ec13715fe0c58ffbf23cf377f0ea5d29d6de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292397"
---
# <a name="secure-user-based-service-accounts-in-active-directory"></a>Active Directory でユーザーベースのサービス アカウントをセキュリティで保護する

オンプレミスのユーザー アカウントを使用することは、Windows 上で動作するサービスをセキュリティで保護するための従来のアプローチです。 グループ管理サービス アカウント (gMSA) やスタンドアロンの管理サービス アカウント (sMSA) がサービスでサポートされていない場合は、これらのアカウントを最後の手段として使用します。 使用するアカウントの最適な種類の選択については、[オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)に関するページを参照してください。 

また、マネージド ID やサービス プリンシパルなどの Azure サービス アカウントを使用するようにサービスを移動できるかどうかを調査することもできます。 

オンプレミスのユーザー アカウントを作成して、アカウントがローカル リソースとネットワーク リソースにアクセスするために必要なサービスとアクセス許可のセキュリティ コンテキストを用意することができます。 他の Active Directory ユーザー アカウントと同様に、オンプレミスのユーザー アカウントには手動のパスワード管理が必要です。 これらのアカウントのセキュリティを維持するために、サービスとドメインの管理者は、強力なパスワード管理プロセスを監視する必要があります。

ユーザー アカウントをサービス アカウントとして作成する場合は、単一のサービスに対してのみ使用します。 それがサービス アカウントであることや、それをどのサービスに使用するかが明確になるように、名前を指定します。 

## <a name="benefits-and-challenges"></a>メリットと課題

オンプレミスのユーザー アカウントには大きな利点があります。 サービスで使用する場合に最も汎用性の高いアカウントの種類です。 サービス アカウントとして使用するユーザー アカウントは、通常のユーザー アカウントを管理するすべてのポリシーによって制御できます。 ただし、MSA を使用できない場合にのみ使用してください。 また、コンピューター アカウントがより適切なオプションであるかどうかも評価します。 

次の表は、オンプレミスのユーザー アカウントの使用に関連する課題をまとめたものです。

| 課題 | 対応策 |
| - | - |
| パスワード管理は手動のプロセスであり、セキュリティの低下やサービスのダウンタイムが発生する可能性があります。| <li>パスワードの複雑さとパスワードの変更が堅牢なプロセスによって管理され、強力なパスワードを使用した定期的な更新が保証されていることを確認します。<li>パスワードの変更をサービスのパスワード更新と合わせることで、サービスのダウンタイムを減らすことができます。 |
| サービス アカウントとして機能しているオンプレミス ユーザー アカウントを識別するのが困難な場合があります。 | <li>環境にデプロイされたサービス アカウントの記録を文書化し、維持します。<li>アカウント名と、それらにアクセスが割り当てられているリソースを追跡します。<li>サービス アカウントとして使用されるすべてのユーザー アカウントに、"svc_" というプレフィックスを追加することを検討してください。 |
| | |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>サービス アカウントとして使用されるユーザー アカウントの検索

オンプレミスのユーザー アカウントは、他の Active Directory ユーザー アカウントと同様です。 サービス アカウントであると識別できるユーザー アカウントの属性がないため、そのようなアカウントを見つけるのは困難な場合があります。 

サービス アカウントとして使用されるすべてのユーザー アカウントに対して、わかりやすい名前付け規則を作成することをお勧めします。 たとえば、プレフィックスとして "svc-" を追加し、サービスに "svc-HRDataConnector" という名前を付けることができます。

次の条件のいくつかを使用して、これらのサービス アカウントを見つけることができます。 ただし、この方法では、次のようなすべてのアカウントを見つけられない可能性があります。

* 委任に対して信頼されているアカウント。  
* アカウントにサービス プリンシパル名がある。  
* パスワードが期限切れにならないように設定されているアカウント。

次の PowerShell コマンドを実行すると、サービス用に作成したオンプレミスのユーザー アカウントを見つけることができます。

委任に対して信頼されているアカウントを見つけるには:

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

サービス プリンシパル名を持つアカウントを見つけるには:

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

期限切れにならないように設定されているパスワードを持つアカウントを見つけるには:

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```

また、機密性の高いリソースへのアクセスを監査し、監査ログをセキュリティ情報イベント管理 (SIEM) システムにアーカイブすることもできます。 Azure Log Analytics や Microsoft Sentinel などのシステムを使用して、サービス アカウントを検索し、分析できます。

## <a name="assess-the-security-of-on-premises-user-accounts"></a>オンプレミス ユーザー アカウントのセキュリティの評価

次の条件を使用して、サービス アカウントとして使用されているオンプレミス ユーザー アカウントのセキュリティを評価できます。

* パスワード管理ポリシーはどのようなものか。  
* アカウントが特権グループのメンバーか。  
* アカウントが重要なリソースに対する読み取りおよび書き込みのアクセス許可を持っているか。

### <a name="mitigate-potential-security-issues"></a>潜在的なセキュリティの問題の軽減

次の表は、オンプレミスのユーザー アカウントに関する潜在的なセキュリティの問題と、その軽減策をまとめたものです。

| セキュリティ上の問題 | 対応策 |
| - | - |
| パスワード管理。| <li>パスワードの複雑さとパスワードの変更が堅牢なプロセスによって管理され、強力なパスワード要件に従った定期的な更新が含まれていることを確認します。<li>サービスのダウンタイムを最小限に抑えるように、パスワード変更とパスワード更新を調整します。 |
| アカウントが特権グループのメンバーの場合。| <li>グループ メンバーシップを確認します。<li>そのアカウントを特権グループから削除します。<li>アカウントに、そのサービスを実行するために必要な権限とアクセス許可のみを付与します (サービス ベンダーに問い合わせてください)。 たとえば、ローカルでのサインインを拒否したり、対話型のサインインを拒否したりできます。 |
| このアカウントには、機密性の高いリソースに対する読み取りおよび書き込みのアクセス許可があります。| <li>機密性の高いリソースへのアクセスを監査します。<li>監査ログを分析のために SIEM (Azure Log Analytics や Microsoft Sentinel) にアーカイブします。<li>望ましくないレベルのアクセスが検出された場合は、リソース アクセス許可を修正します。 |
| | |


## <a name="move-to-more-secure-account-types"></a>より安全な種類のアカウントへの移行

Microsoft では、オンプレミス ユーザー アカウントをサービス アカウントとして使用することはお勧めしていません。 この種類のアカウントを使用するサービスの場合は、代わりに gMSA または sMSA を使用するように構成できるかどうかを評価してください。

さらに、より安全な種類のサービス アカウントを使用できるように、サービス自体を Azure に移行できるかどうかを評価します。 

## <a name="next-steps"></a>次のステップ

サービス アカウントのセキュリティ保護の詳細については、次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)  
* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)  
* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)  
* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)  
* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)

 

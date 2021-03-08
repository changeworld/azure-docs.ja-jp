---
title: Active Directory サービス アカウントの概要 |Azure Active Directory
description: Active Directory でのサービス アカウントの種類の概要と、それらをセキュリティで保護する方法について説明します。
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645618"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory サービス アカウントの概要

サービスには、ローカル リソースとネットワーク リソースのアクセス権を決定する、プライマリ セキュリティ ID があります。 Microsoft Win32 サービスのセキュリティ コンテキストは、サービスを開始するために使用されるサービス アカウントによって決定されます。 サービス アカウントは次の目的で使用されます。
* サービスを識別し、認証する
* サービスを正常に開始する
* コードまたはアプリケーションへのアクセスや実行を行う
* プロセスを開始する 

## <a name="types-of-on-premises-service-accounts"></a>オンプレミス サービス アカウントの種類

ユース ケースに基づいて、管理サービス アカウント (MSA)、コンピューター アカウント、またはユーザー アカウントを使用してサービスを実行できます。 サービスをテストして、管理サービス アカウントを使用できることを確認する必要があります。 可能であれば、1 つを使用する必要があります。

### <a name="group-msa-accounts"></a>グループ MSA アカウント

オンプレミス環境で実行されているサービスで可能な場合は、[グループの管理サービス アカウント](service-accounts-group-managed.md) (gMSA) を使用します。 gMSA は、サーバー ファームで、またはネットワーク ロード バランサーの背後で実行されるサービスに対して、単一の ID ソリューションを提供します。 また、1 台のサーバーで実行されているサービスにも使用できます。 [gMSA には、満たす必要がある特定の要件があります](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

### <a name="standalone-msa-accounts"></a>スタンドアロンの MSA アカウント

gMSA を使用できない場合は、[スタンドアロンの管理サービス アカウント](service-accounts-standalone-managed.md)(sMSA) を使用します。 sMSA を使用するには、Windows Server 2008R2 以降が必要です。 gMSA とは異なり、sMSA は 1 台のサーバーでのみ実行されます。 そのサーバー上の複数のサービスに使用できます。

### <a name="computer-account"></a>コンピューター アカウント

MSA を使用できない場合は、[コンピューター アカウント](service-accounts-computer.md)の使用について調査します。 LocalSystem アカウントは、ローカル コンピューターに対する広範な特権を持ち、ネットワーク上のコンピューター ID として機能する、定義済みのローカル アカウントです。   
LocalSystem アカウントとして実行されるサービスは、<ドメイン名>\<computer_name> の形式で、コンピューター アカウントの資格情報を使用してネットワーク リソースにアクセスします。

NT AUTHORITY\SYSTEM は、LocalSystem アカウント用の定義済みの名前です。 これは、サービスを開始し、そのサービスのセキュリティ コンテキストを提供するために使用できます。

> [!NOTE]
> コンピューター アカウントが使用されている場合、コンピューター上でそのアカウントを使用しているサービスを特定できません。そのため、どのサービスが変更を行っているかを監査できません。 

### <a name="user-account"></a>ユーザー アカウント

MSA を使用できない場合は、[ユーザー アカウント](service-accounts-user-on-premises.md)の使用について調査します。 ユーザー アカウントは、ドメイン ユーザー アカウントまたはローカル ユーザー アカウントです。

ドメイン ユーザー アカウントを使用すると、Windows と Microsoft Active Directory Domain Services のサービス セキュリティ機能をサービスで最大限に活用できます。 サービスでは、そのアカウントにローカルおよびネットワークのアクセス権が付与されます。 また、そのアカウントがメンバーとなっているグループのアクセス許可も付与されます。 ドメイン サービス アカウントは、Kerberos 相互認証をサポートします。

ローカル ユーザー アカウント (名前の形式: ".\UserName") は、ホスト コンピューターの SAM データベースにのみ存在します。Active Directory Domain Services にはユーザー オブジェクトがありません。 ローカル アカウントをドメインで認証することはできません。 そのため、ローカル ユーザー アカウントのセキュリティ コンテキストで実行されるサービスには、ネットワーク リソースへのアクセス権はありません (匿名ユーザーの場合を除く)。 ローカル ユーザー コンテキストで実行されているサービスは、サービスがクライアントによって認証される Kerberos 相互認証をサポートできません。 これらの理由から、ローカル ユーザー アカウントは、通常、ディレクトリ対応サービスには適していません。

> [!IMPORTANT]
> 特権グループのメンバーシップによって、セキュリティ上のリスクとなるアクセス権が付与される可能性があるため、サービス アカウントを特権グループのメンバーにすることはできません。 監査とセキュリティのために、各サービスに独自のサービス アカウントが必要です。

## <a name="choose-the-right-type-of-service-account"></a>適切な種類のサービス アカウントの選択


| 条件| gMSA| sMSA| コンピューター アカウント| ユーザー アカウント |
| - | - | - | - | - |
| 1 台のサーバー上でアプリケーションを実行| はい| 正解です。 可能な場合は gMSA を使用| はい。 可能な場合は MSA を使用| はい。 可能な場合は MSA を使用 |
| 複数のサーバーでアプリケーションを実行| はい| いいえ| いいえ。 アカウントはサーバーに関連付けられる| はい。 可能な場合は MSA を使用 |
| ロード バランサーの背後でアプリケーションを実行| はい| いいえ| いいえ| はい。 gMSA を使用できない場合にのみ使用 |
| Windows Server 2008 R2 でアプリケーションを実行| いいえ| はい| 正解です。 可能な場合は MSA を使用| はい。 可能な場合は MSA を使用 |
| Windows server 2012 で実行| はい| 正解です。 可能な場合は gMSA を使用| はい。 可能な場合は MSA を使用| はい。 可能な場合は MSA を使用 |
| サービス アカウントを 1 台のサーバーに制限する必要がある| いいえ| はい| 正解です。 可能な場合は sMSA を使用| いいえ。 |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>サーバー ログと PowerShell を使用した調査

サーバー ログを使用して、アプリケーションが実行されているサーバーとサーバーの台数を確認できます。

次の PowerShell コマンドを実行すると、ネットワーク上のすべてのサーバーについて、Windows Server のバージョンの一覧を取得できます。 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>オンプレミス サービス アカウントの検索

"Svc" などのプレフィックスを追加することをお勧めします。 サービス アカウントとして使用されるすべてのアカウントに追加します。 この名前付け規則を使用すると、検索と管理が簡単になります。 また、サービス アカウントとサービス アカウントの所有者について説明属性を使用することも検討してください (チーム エイリアスまたはセキュリティ チーム所有者など)。

オンプレミス サービス アカウントを見つけることは、セキュリティを確保するために重要です。 また、MSA 以外のアカウントでは困難な場合があります。 オンプレミスの重要なリソースにアクセスできるすべてのアカウントを確認し、サービス アカウントとして機能しているコンピューター アカウントまたはユーザー アカウントを判断することをお勧めします。 次の方法でアカウントを見つけることもできます。

* 各種アカウントの記事には、そのアカウントの種類を検索するための詳細な手順が記載されています。 これらの記事へのリンクについては、この記事の「次のステップ」の項を参照してください。

## <a name="document-service-accounts"></a>サービス アカウントの文書化

オンプレミス環境にサービス アカウントが見つかったら、各アカウントに関する次の情報を文書化します。 

* 所有者。 アカウントを管理する責任者です。

* 目的。 アカウントが表すアプリケーション、またはその他の目的です。 

* アクセス許可のスコープ。 どのようなアクセス許可がありますか。また、それは妥当ですか。 いずれかのグループのメンバーですか。

* リスク プロファイル。 このアカウントが侵害された場合、ビジネスにどのようなリスクがありますか。 高いリスクがある場合は、MSA を使用します。

* 予想される有効期間と定期的な構成証明。 このアカウントが有効であると予想される期間はどれくらいですか。 どの程度の頻度で、所有者が継続的なニーズを確認し、構成証明を行う必要がありますか。

* パスワードのセキュリティ。 ユーザー アカウントやローカル コンピューター アカウントの場合、パスワードがどこに格納されるかです。 パスワードの安全性を確保し、アクセス権を持つユーザーを文書化します。 格納されたパスワードをセキュリティで保護するには [Privileged Identity Management](../privileged-identity-management/pim-configure.md) の使用を検討してください。 

  

## <a name="next-steps"></a>次のステップ

サービス アカウントのセキュリティ保護に関する次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)

* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)

* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)

* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)

* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)

* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)


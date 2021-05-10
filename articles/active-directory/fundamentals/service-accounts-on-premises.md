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
ms.openlocfilehash: 4551050cd8606c577edbbdfd85debc06ac12020c
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206495"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory サービス アカウントの概要

サービスには、ローカル リソースとネットワーク リソースのアクセス権を決定する、プライマリ セキュリティ ID があります。 Microsoft Win32 サービスのセキュリティ コンテキストは、サービスを開始するために使用されるサービス アカウントによって決定されます。 次の目的でサービス アカウントを使用します。
* サービスを識別し、認証する。
* サービスを正常に開始する。
* コードまたはアプリケーションへのアクセスや実行を行う。
* プロセスを開始する。 

## <a name="types-of-on-premises-service-accounts"></a>オンプレミス サービス アカウントの種類

ユース ケースに応じて、管理されたサービス アカウント (MSA)、コンピューター アカウント、またはユーザー アカウントを使用してサービスを実行できます。 まず、サービスをテストして、管理されたサービス アカウントを使用できるかどうかを確認する必要があります。 サービスで MSA を使用できる場合は、1 つを使用する必要があります。

### <a name="group-managed-service-accounts"></a>グループ管理サービス アカウント

オンプレミス環境で実行されるサービスの場合は、可能な限り[グループの管理されたサービス アカウント (gMSA)](service-accounts-group-managed.md) を使用します。 gMSA では、サーバー ファームで、またはネットワーク ロード バランサーの背後で実行されるサービスに対して、単一の ID ソリューションが提供されます。 gMSA は、1 台のサーバーで実行されるサービスにも使用できます。 gMSA の要件については、「[グループの管理されたサービス アカウントの概要](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)」を参照してください。

### <a name="standalone-managed-service-accounts"></a>スタンドアロンの管理されたサービス アカウント

gMSA を使用できない場合は、[スタンドアロンの管理されたサービス アカウント (sMSA)](service-accounts-standalone-managed.md) を使用します。 sMSA を使用するには、Windows Server 2008 R2 以降が必要です。 gMSA とは異なり、sMSA は 1 台のサーバーでのみ実行されます。 そのサーバー上の複数のサービスに使用できます。

### <a name="computer-accounts"></a>コンピューター アカウント

MSA を使用できない場合は、[コンピューター アカウント](service-accounts-computer.md)の使用を検討してください。 LocalSystem アカウントは、ローカル コンピューターに対する広範な権限を持ち、ネットワーク上のコンピューター ID として機能する定義済みのローカル アカウントです。

LocalSystem アカウントとして実行されるサービスでは、<domain_name>\\<computer_name> の形式でコンピューター アカウントの資格情報を使用してネットワーク リソースにアクセスします。 その定義済みの名前は NT AUTHORITY\SYSTEM です。 それを使用して、サービスを開始し、そのサービスのセキュリティ コンテキストを提供することができます。

> [!NOTE]
> コンピューター アカウントを使う場合、そのアカウントを使用しているコンピューター上のサービスを特定することはできません。 そのため、変更が行われているサービスを監査することはできません。 

### <a name="user-accounts"></a>ユーザー アカウント

MSA を使用できない場合は、[ユーザー アカウント](service-accounts-user-on-premises.md)の使用を検討してください。 ユーザー アカウントは、''*ドメイン*'' ユーザー アカウントまたは ''*ローカル*'' ユーザー アカウントにすることができます。

ドメイン ユーザー アカウントを使用すると、Windows と Microsoft Active Directory Domain Services のサービス セキュリティ機能をサービスで最大限に活用できます。 サービスでは、そのアカウントにローカルおよびネットワークのアクセス許可が付与されます。 また、そのアカウントがメンバーとなっているグループのアクセス許可も付与されます。 ドメイン サービス アカウントは、Kerberos 相互認証をサポートします。

ローカル ユーザー アカウント (名前の形式: *.\UserName*) は、ホスト コンピューターのセキュリティ アカウント マネージャー データベースにのみ存在します。 Active Directory Domain Services にはユーザー オブジェクトはありません。 ローカル アカウントをドメインで認証することはできません。 そのため、ローカル ユーザー アカウントのセキュリティ コンテキストで実行されるサービスには、ネットワーク リソースへのアクセス権はありません (匿名ユーザーの場合を除く)。 ローカル ユーザー コンテキストで実行されるサービスでは、サービスがそのクライアントによって認証される Kerberos 相互認証をサポートできません。 これらの理由から、ローカル ユーザー アカウントは通常、ディレクトリ対応サービスには適していません。

> [!IMPORTANT]
> 特権グループのメンバーシップによって、セキュリティ上のリスクとなるおそれがあるアクセス許可が付与されるため、サービス アカウントを特権グループのメンバーにすることはできません。 監査とセキュリティのために、各サービスに独自のサービス アカウントが必要です。

## <a name="choose-the-right-type-of-service-account"></a>適切な種類のサービス アカウントの選択


| 条件| gMSA| sMSA| コンピューター&nbsp;アカウント| ユーザー&nbsp;アカウント |
| - | - | - | - | - |
| 1 台のサーバー上でアプリを実行| はい| はい。 可能な場合は gMSA を使用します。| はい。 可能な場合は MSA を使用します。| はい。 可能な場合は MSA を使用します。 |
| 複数のサーバーでアプリケーションを実行| はい| いいえ| いいえ。 アカウントはサーバーに関連付けられています。| はい。 可能な場合は MSA を使用します。 |
| ロード バランサーの背後でアプを実行| はい| いいえ| いいえ| はい。 gMSA を使用できない場合にのみ使用します。 |
| Windows Server 2008 R2 でアプリケーションを実行| いいえ| はい| はい。 可能な場合は MSA を使用します。| はい。 可能な場合は MSA を使用します。 |
| Windows Server 2012 上でアプリを実行| はい| はい。 可能な場合は gMSA を使用します。| はい。 可能な場合は MSA を使用します。| はい。 可能な場合は MSA を使用します。 |
| サービス アカウントを 1 台のサーバーに制限する必要がある| いいえ| はい| はい。 可能な場合は sMSA を使用します。| いいえ |
| | |


### <a name="use-server-logs-and-powershell-to-investigate"></a>サーバー ログと PowerShell を使用した調査

サーバー ログを使用して、アプリケーションが実行されているサーバーとサーバーの台数を確認できます。

ネットワーク上のすべてのサーバーについて、Windows Server のバージョンの一覧を取得するために、次の PowerShell コマンドを実行できます。 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>オンプレミス サービス アカウントの検索

サービス アカウントとして使用するすべてのアカウントに "svc-" などのプレフィックスを追加することをお勧めします。 この名前付け規則を使用すると、アカウントの検索と管理がより簡単になります。 サービス アカウントとサービス アカウントの所有者について、説明属性を使用することも検討してください。 説明は、チーム エイリアスまたはセキュリティ チーム所有者にすることができます。

オンプレミス サービス アカウントを見つけることは、セキュリティを確保するために重要です。 この作業は、MSA 以外のアカウントでは困難な場合があります。 オンプレミスの重要なリソースにアクセスできるすべてのアカウントを確認し、サービス アカウントとして機能する可能性のあるコンピューターまたはユーザー アカウントを判断することをお勧めします。 

サービス アカウントを見つける方法については、[「次のステップ」セクション](#next-steps)のそのアカウントの種類に関する記事を参照してください。

## <a name="document-service-accounts"></a>サービス アカウントの文書化

オンプレミス環境でサービス アカウントを見つけた後、次の情報を文書化します。 

* **所有者**: アカウントを管理する責任者。

* **目的**: アカウントが表すアプリケーション、またはその他の目的。 

* **アクセス許可スコープ**: 所有している、または所有する必要があるアクセス許可、およびそのメンバーであるすべてのグループ。

* **リスク プロファイル**: このアカウントが侵害された場合のビジネスのリスク。 リスクが高い場合は、MSA を使用します。

* **予想される有効期間と定期的な構成証明**: このアカウントが有効であると予想される期間、および所有者が継続的なニーズを確認し、構成証明する頻度。

* **パスワードのセキュリティ**: ユーザーおよびローカル コンピューター アカウントの場合、パスワードがどこに格納されるか。 パスワードの安全性を確保し、アクセス権を持つユーザーを文書化します。 格納されたパスワードをセキュリティで保護するには [Privileged Identity Management](../privileged-identity-management/pim-configure.md) の使用を検討してください。 

  

## <a name="next-steps"></a>次のステップ

サービス アカウントのセキュリティ保護の詳細については、次の記事をご覧ください。

* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)  
* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)  
* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)  
* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)  
* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)


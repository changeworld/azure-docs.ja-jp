---
title: コンピューター アカウントをセキュリティで保護する | Azure Active Directory
description: オンプレミス コンピューター アカウントのセキュリティ保護を支援するためのガイド。
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
ms.openlocfilehash: ec2e8cddb93e2e6fdf9ff804bbc28fd283436131
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206621"
---
# <a name="secure-on-premises-computer-accounts"></a>オンプレミス コンピューター アカウントをセキュリティで保護する

コンピューター アカウント (LocalSystem アカウント) は、ローカル コンピューター上の実質的にすべてのリソースにアクセスできる、高い特権を持つ組み込みのアカウントです。 このアカウントは、サインオンしたユーザー アカウントに関連付けられていません。 LocalSystem として実行されているサービスは、<domain_name>\\<computer_name>$ の形式でコンピューターの資格情報をリモート サーバーに提示することによって、ネットワーク リソースにアクセスします。 コンピューター アカウントの定義済みの名前は NT AUTHORITY\SYSTEM です。 これを使用して、サービスを開始し、そのサービスのセキュリティ コンテキストを提供することができます。

![コンピューター アカウント上のローカル サービスの一覧のスクリーンショット。](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-a-computer-account"></a>コンピューター アカウントを使用する利点

コンピューター アカウントには、次のような利点があります。

* **無制限のローカル アクセス**:コンピューター アカウントでは、コンピューターのローカル リソースに対して完全なアクセス権が付与されます。

* **自動的なパスワード管理**:パスワードを手動で変更する必要がありません。 このアカウントは Active Directory のメンバーであり、アカウントのパスワードは自動的に変更されます。 コンピューター アカウントを使用すると、サービスのサービス プリンシパル名を登録する必要がなくなります。

* **コンピューター外部での制限付きアクセス権**:Active Directory Domain Services (AD DS) の既定のアクセス制御リストでは、コンピューター アカウントに対して最小限のアクセスが許可されます。 承認されていないユーザーがアクセスした場合、サービスには、ネットワーク上のリソースへの制限付きアクセス権のみが付与されます。

## <a name="assess-the-security-posture-of-computer-accounts"></a>コンピューター アカウントのセキュリティ体制を評価する

次の表に、コンピューター アカウントを使用する場合の潜在的な問題と関連する軽減策を示します。
 
| 問題 | 対応策 |
| - | - |
| コンピューターがドメインから離脱し、再度参加すると、コンピューター アカウントが削除され、再作成される可能性がある。 | コンピューターを Active Directory グループに追加する必要があるかどうかを検証し、この記事の次のセクションにあるサンプル スクリプトを使用して、どのコンピューター アカウントがグループに追加されているかを確認します。| 
| コンピューター アカウントをグループに追加すると、そのコンピューターで LocalSystem として実行されているすべてのサービスに、そのグループのアクセス権が付与される。| コンピューター アカウントのグループ メンバーシップに注意してください。 コンピューター アカウントをドメイン管理者グループのメンバーにすることは避けてください。これは、関連付けられたサービスには AD DS への完全なアクセス権があるためです。 |
| LocalSystem のネットワークの既定値が正しくない。 | コンピューター アカウントに、ネットワーク リソースへの既定の制限付きアクセス権があると想定しないでください。 代わりに、このアカウントのグループ メンバーシップを慎重に確認してください。 |
| 不明なサービスが LocalSystem として実行されている。 | LocalSystem アカウントで実行されているすべてのサービスが、Microsoft サービスまたはサードパーティの信頼できるサービスであることを確認します。 |
| | |

## <a name="find-services-that-run-under-the-computer-account"></a>コンピューター アカウントで実行されているサービスを検索する

LocalSystem コンテキストで実行されているサービスを検索するには、次の PowerShell コマンドレットを使用します。

```powershell
Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

特定のグループのメンバーであるコンピューター アカウントを検索するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

ID 管理者グループ (ドメイン管理者、エンタープライズ管理者、管理者) のメンバーであるコンピューター アカウントを検索するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```

## <a name="move-from-computer-accounts"></a>コンピューター アカウントから移動する

> [!IMPORTANT]
> コンピューター アカウントは高い特権を持つアカウントです。サービスがコンピューター上のローカル リソースに対して無制限のアクセスを必要とし、管理サービス アカウント (MSA) を使用できない場合にのみ使用してください。

* MSA を使用してサービスを実行できるかどうかをサービスの所有者に確認し、サービスでサポートされている場合は、グループ管理サービス アカウント (gMSA) またはスタンドアロン管理サービス アカウント (sMSA) を使用します。

* サービスを実行するために必要なアクセス許可のみを持つドメイン ユーザー アカウントを使用します。

## <a name="next-steps"></a>次のステップ 

サービス アカウントのセキュリティ保護の詳細については、次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)
* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)
* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)
* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)  
* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)

---
title: スタンドアロンの管理サービス アカウントのセキュリティ保護 | Azure Active Directory
description: スタンドアロンの管理サービス アカウントをセキュリティで保護するためのガイド。
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
ms.openlocfilehash: 8237e746e6f48640ed1aca8d2637eb128172a297
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292435"
---
# <a name="secure-standalone-managed-service-accounts"></a>スタンドアロン管理サービス アカウントをセキュリティで保護する

スタンドアロンの管理サービス アカウント (sMSA) は、サーバーで実行されている 1 つ以上のサービスをセキュリティで保護するために使用する、マネージド ドメイン アカウントです。 複数のサーバーにわたって再利用することはできません。 sMSA は、パスワードの自動管理、簡略化されたサービス プリンシパル名 (SPN) の管理、および他の管理者に管理を委任する機能を提供します。 

Active Directory では、sMSA は、サービスを実行する特定のサーバーに関連付けられています。 これらのアカウントは、Microsoft 管理コンソールの [Active Directory ユーザーとコンピューター] スナップインにリストされています。

![管理サービス アカウント OU を示す [Active Directory ユーザーとコンピューター] スナップインのスクリーン ショット。](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

管理サービス アカウントは、Windows Server 2008 R2 Active Directory スキーマで導入されたものであり、Windows Server 2008 R2 以降が必要です。 

## <a name="benefits-of-using-smsas"></a>sMSA を使用する利点

sMSA は、サービス アカウントとして使用されるユーザー アカウントよりも高度なセキュリティを提供します。 同時に、管理オーバーヘッドを削減するために、次のことを行います。

* **強力なパスワードの設定**: sMSA では、240 バイトのランダムに生成された複雑なパスワードが使用されます。 sMSA パスワードの複雑さと長さにより、ブルート フォースや辞書攻撃によってサービスが侵害される可能性が最小限に抑えられます。

* **パスワードの定期的な循環**: Windows によって、sMSA パスワードが 30 日ごとに自動的に変更されます。 サービス管理者とドメイン管理者が、パスワードの変更をスケジュールしたり、関連するダウンタイムを管理したりする必要はありません。

* **SPN 管理の簡素化**: ドメインの機能レベル (DFL) が Windows Server 2008 R2 の場合、サービス プリンシパル名は自動的に更新されます。 たとえば、次のことを行うと、サービス プリンシパル名が自動的に更新されます。
   * ホスト コンピューター アカウントを変更する。  
   * ホスト コンピューターのドメイン ネーム サーバー (DNS) 名を変更する。  
   * [PowerShell](/powershell/module/activedirectory/set-adserviceaccount) を使用して、他の sam-accountname パラメーターまたは dns-hostname パラメーターを追加または削除する。

## <a name="when-to-use-smsas"></a>sMSA を使用する場合

sMSA を使用すると、管理タスクとセキュリティ タスクを簡単に実行できます。 sMSA は、1 台のサーバーに 1 つ以上のサービスがデプロイされていて、グループ管理サービス アカウント (gMSA) を使用できない場合に使用します。 

> [!NOTE] 
> 複数のサービスに対して sMSA を使用できますが、監査のために、各サービスに独自の ID を設定することをお勧めします。 

MSA を使用できるかどうかをソフトウェアの作成者が判断できない場合は、アプリケーションをテストする必要があります。 これを行うには、テスト環境を作成し、必要なすべてのリソースにアクセスできることを確認します。 詳細については、[sMSA の作成とインストール](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting)に関するページを参照してください。

### <a name="assess-the-security-posture-of-smsas"></a>sMSA のセキュリティ体制を評価する

sMSA は、継続的なパスワード管理を必要とする標準ユーザー アカウントよりも本質的に安全性が高くなります。 ただし、全体的なセキュリティ体制の一部として、sMSA のアクセス スコープを考慮することが重要です。

sMSA によって生じる可能性のあるセキュリティの問題を軽減する方法については、次の表を参照してください。

| セキュリティ上の問題| 対応策 |
| - | - |
| sMSA は特権グループのメンバーです。 | <li>昇格された特権グループ (Domain Admins など) から sMSA を削除します。<li>"*最小特権*" モデルを使用し、sMSA にそのサービスを実行するために必要な権限とアクセス許可のみを付与します。<li>必要なアクセス許可がわからない場合は、サービス作成者に問い合わせてください。 |
| sMSA には、機密リソースへの読み取りおよび書き込みアクセス権があります。 | <li>機密性の高いリソースへのアクセスを監査します。<li>分析のために、監査ログを Azure Log Analytics や Microsoft Sentinel などのセキュリティ情報イベント管理 (SIEM) プログラムにアーカイブします。<li>望ましくないレベルのアクセスが検出された場合は、リソース アクセス許可を修正します。 |
| 既定では、sMSA パスワード ロールオーバーの頻度は 30 日です。 | グループ ポリシーを使用して、企業のセキュリティ要件に応じて期限を調整することができます。 パスワードの有効期限を設定するには、次のパスを使用します。<br>*コンピューターの構成\ポリシー\Windows の設定\セキュリティ設定\セキュリティ オプション*。 ドメイン メンバーの場合は、 **[マシン アカウントのパスワード変更の最大有効期間]** を使用します。 |
| | |



### <a name="challenges-with-smsas"></a>sMSA に関する課題

sMSA に関連する課題は次のとおりです。

| 課題| 対応策 |
| - | - |
| sMSA は 1 台のサーバーでしか使用できません。 | 複数のサーバーにわたってアカウントを使用する必要がある場合は、gMSA を使用します。 |
| sMSA は複数のドメイン間では使用できません。 | 複数のドメインにわたってアカウントを使用する必要がある場合は、gMSA を使用します。 |
| すべてのアプリケーションで sMSA がサポートされるわけではありません。 | 可能な場合は gMSA を使用します。 可能でない場合は、アプリケーションの作成者が勧める標準のユーザー アカウントまたはコンピューター アカウントを使用します。 |
| | |


## <a name="find-smsas"></a>sMSA の検索

任意のドメイン コントローラーで、DSA.msc を実行してから、管理サービス アカウントのコンテナーを展開して、すべての sMSA を表示します。 

Active Directory ドメイン内のすべての sMSA と gMSA を返すには、次の PowerShell コマンドを実行します。 

`Get-ADServiceAccount -Filter *`

Active Directory ドメイン内の sMSA のみを返すには、次のコマンドを実行します。

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>sMSA の管理

sMSA を管理するには、次の Active Directory PowerShell コマンドレットを使用できます。

`Get-ADServiceAccount`
`Install-ADServiceAccount`
`New-ADServiceAccount`
`Remove-ADServiceAccount`
`Set-ADServiceAccount`
`Test-ADServiceAccount`
`Uninstall-ADServiceAccount`

## <a name="move-to-smsas"></a>sMSA への移行

アプリケーション サービスで sMSA はサポートされているが gMSA がサポートされておらず、現在、セキュリティ コンテキストにユーザー アカウントまたはコンピューター アカウントを使用している場合は、サーバーに [sMSA を作成してインストール](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting)します。 

理想としては、Azure にリソースを移動し、Azure のマネージド ID またはサービス プリンシパルを使用します。

## <a name="next-steps"></a>次のステップ

サービス アカウントのセキュリティ保護の詳細については、次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)  
* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)  
* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)  
* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)  
* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)

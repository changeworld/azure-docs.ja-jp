---
title: グループ管理サービス アカウントをセキュリティで保護する | Azure Active Directory
description: グループ管理サービス アカウント (gMSA) のコンピューター アカウントのセキュリティ保護に関するガイド。
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
ms.openlocfilehash: f934e016f23e988cbe8673f80e96c4583b6aaec5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292511"
---
# <a name="secure-group-managed-service-accounts"></a>グループ管理サービス アカウントをセキュリティで保護する

グループ管理サービス アカウント (gMSA) は、サービスのセキュリティ保護に使用するマネージド ドメイン アカウントです。 gMSA は、1 台のサーバー上、またはサーバー ファーム (ネットワーク負荷分散やインターネット インフォメーション サービス (IIS) サーバーの背後にあるシステムなど) で実行できます。 gMSA プリンシパルを使用するようにサービスを構成すると、そのアカウントのパスワード管理は Windows オペレーティング システムによって処理されます。

## <a name="benefits-of-using-gmsas"></a>gMSA を使用する利点

gMSA には、セキュリティが強化された 1 つの ID ソリューションが用意されています。 同時に、管理オーバーヘッドを削減するために、次の機能があります。

* **強力なパスワードを設定する**: gMSA には、ランダムに生成された 240 バイトの複雑なパスワードが使用されています。 gMSA パスワードの複雑さと長さにより、ブルート フォースや辞書攻撃によってサービスが侵害される可能性が最小限に抑えられます。

* **パスワードを定期的に循環させる**: パスワード管理は gMSA から Windows オペレーティング システムに移行されます。これにより、30 日ごとにパスワードが変更されます。 サービスおよびドメインの管理者は、サービス アカウントのセキュリティを維持するために、パスワードの変更をスケジュールしたり、サービスの停止を管理したりする必要がなくなりました。 

* **サーバー ファームへのデプロイのサポート**: gMSA を複数のサーバーにデプロイする機能を使用すると、複数のホストで同じサービスが実行される負荷分散ソリューションをサポートできます。 

* **簡略化されたサービス プリンシパル名 (SPN) 管理のサポート**: アカウントの作成時に PowerShell を使用して SPN を設定できます。 また、gMSA のアクセス許可が正しく設定されていれば、自動 SPN 登録がサポートされているサービスでは、gMSA に対してそれを行うことができます。 

## <a name="when-to-use-gmsas"></a>どのような場合に gMSA を使用するか

フェールオーバー クラスタリングなどのサービスでサポートされていない場合を除き、gMSA はオンプレミス サービスの優先アカウントの種類として使用します。

> [!IMPORTANT]
> 運用環境にデプロイする前に、gMSA を使用してサービスをテストする必要があります。 これを行うには、テスト環境を設定し、アプリケーションが gMSA を使用できること、およびアクセスする必要があるリソースにアクセスできることを確認します。 詳細については、「[グループ管理サービス アカウントのサポート](/system-center/scom/support-group-managed-service-accounts)」を参照してください。


サービスで gMSA の使用がサポートされていない場合、次に最適な選択肢は、スタンドアロン管理サービス アカウント (sMSA) を使用することです。 sMSA では gMSA と同じ機能が提供されますが、単一サーバーでのデプロイのみを対象としています。

サービスによってサポートされている gMSA または sMSA を使用できない場合、標準ユーザー アカウントとして実行するようにサービスを構成する必要があります。 アカウントのセキュリティを維持するために、サービスとドメインの管理者は強力なパスワード管理プロセスを監視する必要があります。

## <a name="assess-the-security-posture-of-gmsas"></a>gMSA のセキュリティ体制を評価する

gMSA アカウントは、継続的なパスワード管理を必要とする標準ユーザー アカウントよりも本質的に安全性が高くなります。 ただし、全体的なセキュリティ体制を見るときには、gMSA のアクセス スコープを考慮することが重要です。

gMSA を使用する場合の潜在的なセキュリティ問題とその軽減策を次の表に示します。

| セキュリティ上の問題| 対応策 |
| - | - |
| gMSA は特権グループのメンバーです。 | <li>グループ メンバーシップを確認します。 これを行うには、すべてのグループ メンバーシップを列挙する PowerShell スクリプトを作成します。 次に、gMSA ファイルの名前で結果の CSV ファイルをフィルター処理できます。<li>特権グループから gMSA を削除します。<li>gMSA に、そのサービスを実行するために必要な権限とアクセス許可のみを付与します (サービス ベンダーに問い合わせてください)。 
| gMSA には、機密リソースへの読み取りおよび書き込みアクセス権があります。 | <li>機密リソースへのアクセスを監査します。<li>監査ログを分析のために SIEM (Azure Log Analytics や Microsoft Sentinel など) にアーカイブします。<li>望ましくないレベルのアクセスを検出する場合は、不要なリソース アクセス許可を削除します。 |
| | |


## <a name="find-gmsas"></a>gMSA を検出する

組織が既に gMSA を作成している可能性があります。 これらのアカウントを取得するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-ADServiceAccount 
Install-ADServiceAccount 
New-ADServiceAccount 
Remove-ADServiceAccount 
Set-ADServiceAccount 
Test-ADServiceAccount 
Uninstall-ADServiceAccount
```


gMSA は、効果的に機能するには、管理サービス アカウントの組織単位 (OU) に含まれている必要があります。

  
![管理サービス アカウント OU の gMSA アカウントのスクリーンショット。](./media/securing-service-accounts/secure-gmsa-image-1.png)

この一覧にない可能性のあるサービス MSA を見つけるには、次のコマンドを実行します。

```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all managed service accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where-object {$_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>gMSA を管理する

gMSA アカウントを管理するには、次の Active Directory PowerShell コマンドレットを使用できます。

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Windows Server 2012 以降、*-ADServiceAccount コマンドレットは既定で gMSA で動作します。 上記のコマンドレットの使用の詳細については、[グループ管理サービス アカウントの概要](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)に関するページを参照してください。

## <a name="move-to-a-gmsa"></a>gMSA に移動する
gMSA アカウントは、オンプレミスのニーズにとって最も安全な種類のサービス アカウントです。 それに移動できる場合は、そうすることをお勧めします。 さらに、お使いのサービスを Azure に移動し、お使いのサービス アカウントを Azure Active Directory に移動することもご検討ください。 gMSA アカウントに移動するには、次の手順を実行します。

1. [キー配布サービス (KDS) のルート キー](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)がフォレストにデプロイされていることを確認します。 これは 1 回限りの操作です。

1. [新しい gMSA を作成します](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

1. サービスを実行する各ホストに新しい gMSA をインストールします。
   > [!NOTE] 
   > ホスト上での gMSA の作成とインストールの詳細については、gMSA を使用するようにサービスを構成する前に、[グループ管理サービス アカウントの概要](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))に関する記事を参照してください。

1. お使いのサービス ID を gMSA に変更し、空白のパスワードを指定します。

1. お使いのサービスが新しい gMSA ID で動作していることを確認します。

1. 古いサービス アカウント ID を削除します。

 

## <a name="next-steps"></a>次のステップ

サービス アカウントのセキュリティ保護の詳細については、次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)  
* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)  
* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)  
* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)  
* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)

---
title: Azure Stack での SQL ホスティング サーバー | Microsoft Docs
description: SQL アダプター リソースプロバイダーを使用したプロビジョニングのために SQL インスタンスを追加する方法です。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265230"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>SQL リソース プロバイダーへのホスティング サーバーの追加

SQL リソース プロバイダーが SQL インスタンスに接続できる限り、その SQL インスタンスを、[Azure Stack](azure-stack-poc.md) 内の仮想マシン (VM) 上、または Azure Stack 環境の外部にある VM 上でホストできます。

## <a name="overview"></a>概要

SQL ホスティング サーバーの一般的な要件は次のとおりです。

* SQL インスタンスは、リソース プロバイダーとユーザー ワークロードでのみ使用できるようにする必要があります。 他のコンシューマーが使用中の SQL インスタンスは使用できません。 この制限は、App Services にも適用されます。
* SQL リソース プロバイダーの VM はドメインに参加しておらず、SQL 認証によってのみ接続できます。
* リソース プロバイダーで使用するために、適切な特権を設定してアカウントを構成する必要があります。
* リソース プロバイダーや、Web アプリなどのユーザーはユーザー ネットワークを使用するため、このネットワーク上の SQL インスタンスへの接続が必要となります。 通常、この要件により、使用する SQL インスタンスの IP はパブリック ネットワーク上になくてはならないことになります。
* SQL インスタンスとそのホストの管理は、ユーザーの責任です。 たとえば、リソース プロバイダーは、更新プログラムの適用、バックアップの処理、または資格情報のローテーションの処理は行いません。
* パフォーマンスや AlwaysOn を使用する高可用性など、SQL の異なる能力クラスをサポートする SKU を使うことができます。

### <a name="sql-server-virtual-machine-images"></a>SQL Server 仮想マシンのイメージ

Marketplace 管理機能により、SQL IaaS 仮想マシン イメージを使用できます。 これらのイメージは、Azure で使用できる SQL VM と同じです。

Marketplace 項目を使用して VM をデプロイする前に、必ず **SQL IaaS 拡張機能**の最新バージョンをダウンロードしてください。  IaaS 拡張機能や対応するポータル拡張機能により、修正プログラムの自動適用やバックアップなどの追加機能が提供されます。

SQL VM のデプロイにはその他の選択肢もあります。[Azure Stack Quickstart Gallery](https://github.com/Azure/AzureStack-QuickStart-Templates) のテンプレートなどです。

> [!NOTE]
> 複数ノードの Azure Stack にインストールされるホスティング サーバーは、ユーザー サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 ユーザー ポータル、または適切にログインされた PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーが課金対象の VM であり、適切な SQL ライセンスが必要です。 サービス管理者は、そのサブスクリプションの所有者になることが_できます_。

### <a name="required-privileges"></a>必要な特権

SQL sysadmin より低い特権を持つ管理ユーザーを作成できます。 そのようなユーザーには、次の操作のためのアクセス許可だけが必要です。

- データベース: 作成、変更、含有 (Always On のみ)、ドロップ、バックアップ
- 可用性グループ: データベースの変更、結合、追加/削除
- ログイン: 作成、選択、変更、ドロップ、取り消し
- 操作の選択: \[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>スタンドアロンのホスティング SQL Server に接続して容量を提供する

SQL Server 2014 または SQL Server 2016 の任意のエディションを使って、スタンドアロンの (非 HA) SQL Server を使用できます。 sysadmin 特権を持つアカウントの資格情報があることを確認してください。

既に設定されているスタンドアロンのホスティング サーバーを追加するには、次の手順のようにします。

1. Azure Stack オペレーター ポータルにサービス管理者としてサインインします

2. **[参照]** &gt; **[管理リソース]** &gt; **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** の順に選びます。

   ![SQL ホスティング サーバー](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** では、リソース プロバイダーのバックエンドとして機能する SQL Server のインスタンスに SQL リソース プロバイダーを接続できます。

   ![SQL アダプター ダッシュボード](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. SQL Server インスタンスの接続詳細をフォームに入力します。

   ![SQL ホスティング サーバーを追加する](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    必要に応じて、インスタンス名を含めることができ、インスタンスが既定のポート 1433 に割り当てられていない場合はポート番号を指定できます。

   > [!NOTE]
   > ユーザーと管理者の Azure Resource Manager が SQL インスタンスにアクセスできる限り、SQL インスタンスをリソースプロバイダーの管理下に置くことができます。 SQL インスタンスは、リソース プロバイダーに排他的に割り当てる__必要があります__。

4. サーバーを追加する際に、サービス内容を区別するために新規または既存の SKU に割り当てる必要があります。 たとえば、次のものを提供する SQL Enterprise インスタンスを使用できます。
  
   - データベース容量
   - 自動バックアップ
   - 各部門のための高性能なサーバーの予約

   SKU 内のすべてのホスティング サーバーの機能が同じである必要があります。 ユーザーが各自のデータベースを適切な SKU に展開できるように、**名前** は SKU のプロパティを反映している必要があります。

   > [!IMPORTANT]
   > SQL と MySQL リソース プロバイダーの SKU を作成する場合、**[ファミリ]** または **[層]** 名では、スペースやピリオドなどの特殊文字はサポートされていません。

   例: 

   ![SKU を作成する](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU の作成が完了するまで、ユーザーはデータベースを作成できません。

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>SQL Always On 可用性グループを使用して高可用性を提供する

SQL Always On インスタンスの構成にはさらに手順が必要で、少なくとも 3 台の VM (または物理コンピューター) が必要です。この記事は、Always On 可用性グループについて十分に理解していることを前提にしています。 詳細については、次を参照してください。

* [Azure Virtual Machines での SQL Server Always On 可用性グループの概要](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On 可用性グループ (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL アダプター リソース プロバイダーは、Always On に対して SQL 2016 SP1 Enterprise 以降のインスタンス "_のみ_" をサポートします。 このアダプターの構成には、自動シード処理などの SQL の新機能が必要です。

前記の要件の一覧に加えて、SQL Server のインスタンスごとに各可用性グループで[自動シード処理](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)を有効にする必要があります。

すべてのインスタンスで自動シード処理を有効にするには、次の SQL コマンドを編集し、インスタンスごとに実行します。

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

セカンダリ インスタンスでは、次の SQL コマンドを編集し、インスタンスごとに実行します。

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>SQL Always On ホスティング サーバーを追加するには

1. Azure Stack 管理ポータルにサービス管理者としてサインインします

2. **[参照]** &gt; **[管理リソース]** &gt; **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** &gt; **[+ 追加]** の順に選びます。

   **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** では、リソース プロバイダーのバックエンドとして機能する SQL Server の実際のインスタンスに SQL Server リソース プロバイダーを接続できます。

3. SQL Server インスタンスの接続詳細をフォームに入力します。 Always On リスナーの FQDN アドレス (および必要に応じてポート番号) を使用していることを確認します。sysadmin 特権で構成したアカウントの情報を提供します。

4. SQL Always On 可用性グループ ボックスのインスタンスのサポートを有効にするには、[Always On 可用性グループ] ボックスをオンにします。

   ![Always On を有効にする](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. SKU に SQL Always On インスタンスを追加します。

   > [!IMPORTANT]
   > 同じ SKU にスタンドアロン サーバーと Always On インスタンスを混在させることはできません。 最初のホスティング サーバーを追加した後に型を混在させようとすると、エラーになります。

## <a name="make-the-sql-databases-available-to-users"></a>ユーザーが SQL データベースを使用できるようにする

ユーザーが SQL データベースを使用できるようにするプランとオファーを作成します。 プランに **Microsoft.SqlAdapter** サービスを追加し、既定のクォータを追加するか新しいクォータを作成します。

![データベースに含めるプランとサービスの作成](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>次の手順

[データベースを追加する](azure-stack-sql-resource-provider-databases.md)

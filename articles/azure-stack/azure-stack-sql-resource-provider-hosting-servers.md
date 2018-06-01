---
title: Azure Stack での SQL ホスティング サーバー | Microsoft Docs
description: SQL アダプター リソースプロバイダーを使用したプロビジョニングのための SQL インスタンスを追加する方法
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.openlocfilehash: a89e5bf48c24abf72f18ee98f2dcb0eda6db35cd
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33202594"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>SQL リソース プロバイダーへのホスティング サーバーの追加
[Azure Stack](azure-stack-poc.md) 内の VM 上の SQL インスタンスを使用するか、またはリソースプロバイダーに接続できる場合には Azure Stack 環境外のインスタンスを使用できます。 一般的な要件は次のとおりです。

* SQL インスタンスは、RP とユーザー ワークロードでのみ使用できるようにする必要があります。 他のコンシューマーが使用中の SQL インスタンス (App Services など) は使用できません。
* SQL リソース プロバイダーの VM はドメインに参加しておらず、SQL 認証によってのみ接続できます。
* リソース プロバイダーで使用するために、適切な特権を設定してアカウントを構成する必要があります。
* リソース プロバイダーや、Web アプリなどのユーザーはユーザー ネットワークを使用するため、このネットワーク上の SQL インスタンスへの接続が必要となります。 通常、この要件により、使用する SQL インスタンスの IP はパブリック ネットワーク上になくてはならないことになります。
* SQL インスタンスとそのホストは自由に管理できます。ただし、リソース プロバイダーは修正プログラムの適用、バックアップ、資格情報のローテーションなどを行いません。
* SKU は、パフォーマンス、Always On などのさまざまなクラスの SQL 機能を作成するのに使用できます。

Marketplace 管理機能により、多数の SQL IaaS 仮想マシン イメージを使用できます。 Marketplace 項目を使用して VM をデプロイする前に、必ず **SQL IaaS 拡張機能**の最新バージョンをダウンロードしてください。 これらの SQL イメージは、Azure で使用できる SQL VM と同じです。 これらのイメージから作成された SQL VM の場合、IaaS 拡張機能や対応するポータル拡張機能により、修正プログラムの自動適用やバックアップなどの機能が提供されます。

SQL VM のデプロイにはその他の選択肢もあります。[Azure Stack Quickstart Gallery](https://github.com/Azure/AzureStack-QuickStart-Templates) のテンプレートなどです。

> [!NOTE]
> 複数ノードの Azure Stack にインストールされるホスティング サーバーは、ユーザー サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 ユーザー ポータル、または適切にログインされた PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーが課金対象の VM であり、適切な SQL ライセンスが必要です。 サービス管理者は、そのサブスクリプションの所有者になることが_できます_。


### <a name="required-privileges"></a>必要な特権

sysadmin の特権の一部のみを持つ新しい管理ユーザーを作成できます。 許可が必要な特定の操作は次のとおりです。

- データベース: 作成、変更、含有 (Always On のみ)、ドロップ、バックアップ
- 可用性グループ: データベースの変更、結合、追加/削除
- ログイン: 作成、選択、変更、ドロップ、取り消し
- 操作の選択: \[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>スタンドアロンのホスティング SQL Server に接続して容量を提供する
SQL Server 2014 または SQL Server 2016 の任意のエディションを使って、スタンドアロンの (非 HA) SQL Server を使用できます。 システム管理特権を持つアカウントの資格情報があることを確認してください。

既にプロビジョニングされているスタンドアロンのホスティング サーバーを追加するには、次の手順を実行します。

1. Azure Stack 管理ポータルにサービス管理者としてサインインします

2. **[参照]** &gt; **[管理リソース]** &gt; **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** の順にクリックします。

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** ブレードでは、リソース プロバイダーのバックエンドとして機能する SQL Server の実際のインスタンスに SQL Server リソース プロバイダーを接続できます。

  ![ホスティング サーバー](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. SQL Server インスタンスの接続詳細をフォームに入力します。

  ![新しいホスティング サーバー](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    オプションでインスタンス名を含めることができます。インスタンスが既定のポート 1433 に割り当てられていない場合は、ポート番号を指定できます。

  > [!NOTE]
  > ユーザーと管理者の Azure Resource Manager が SQL インスタンスにアクセスできる限り、SQL インスタンスをリソースプロバイダーの管理下に置くことができます。 SQL インスタンスは RP に排他的に割り当てられている__必要があります__。

4. サーバーを追加する際に、サービス内容を区別するために新規または既存の SKU に割り当てる必要があります。 たとえば、SQL Enterprise インスタンスで次のものを提供できます。
  - データベース容量
  - 自動バックアップ
  - 各部門のための高性能なサーバーの予約
  - その他にもあります。

  ユーザーが各自のデータベースを適切に配置できるように、SKU 名には特性を反映させる必要があります。 SKU 内のすべてのホスティング サーバーの機能が同じである必要があります。

> [!IMPORTANT]
> SQL と MySQL リソース プロバイダーの SKU を作成する場合、**[ファミリ]** または **[層]** 名では、スペースやピリオドなどの特殊文字はサポートされていません。

例:

![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU の作成が完了するまで、ユーザーはデータベースを作成できません。

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>SQL Always On 可用性グループを使用して容量を提供する
SQL Always On インスタンスの構成にはさらに手順が必要で、少なくとも 3 つの VM (または物理マシン) が含まれます。

> [!NOTE]
> SQL アダプター RP では、Always On では SQL 2016 SP1 Enterprise 以降のインスタンス_のみ_がサポートされています。それは、自動シード処理などの新しい SQL 機能が必要なためです。 上記の一般的な要件に加え、次のような要件があります。

* SQL Always On コンピューターだけでなく、ファイル サーバーも準備する必要がある。 この環境を独自に作成できる [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha)がある。 これは、独自のインスタンスを構築するためのガイドとしても役立ちます。

* SQL Server をセットアップする必要がある。 具体的には、SQL Server のインスタンスごとに各可用性グループで[自動シード](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)を有効にする必要があります。

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

セカンダリ インスタンス上
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



SQL Always On のホスティング サーバーを追加するには、次の手順を実行します。

1. Azure Stack 管理ポータルにサービス管理者としてサインインします

2. **[参照]** &gt; **[管理リソース]** &gt; **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** &gt; **[+追加]** の順にクリックします。

    **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** ブレードでは、リソース プロバイダーのバックエンドとして機能する SQL Server の実際のインスタンスに SQL Server リソース プロバイダーを接続できます。


3. 使用する SQL Server インスタンスの接続詳細をフォームに入力します。必ず、Always On リスナーの FQDN または IPv4 アドレス (およびオプションのポート番号) を使用してください。 システム管理特権で構成したアカウントのアカウント情報を提供します。

4. SQL Always On 可用性グループのインスタンスのサポートを有効にするには、このボックスをオンにします。

    ![ホスティング サーバー](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. SKU に SQL Always On インスタンスを追加します。 同じ SKU にスタンドアロン サーバーと Always On インスタンスを混在させることはできません。 最初のホスティング サーバーを追加するときにこれが決まります。 後でタイプを混在させようとするとエラーが発生します。


## <a name="making-sql-databases-available-to-users"></a>ユーザーが SQL データベースを使用できるようにする

ユーザーが SQL データベースを使用できるようにするプランとオファーを作成します。 プランに Microsoft.SqlAdapter サービスを追加し、既存のクォータを追加するか新しいクォータを作成します。 クォータを作成する場合、ユーザーに許可する容量を指定します。

![データベースに含めるプランとサービスの作成](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>次の手順

[データベースを追加する](azure-stack-sql-resource-provider-databases.md)

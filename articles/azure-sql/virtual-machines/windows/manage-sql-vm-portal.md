---
title: Azure portal を使用して Azure 内の SQL Server 仮想マシンを管理する | Microsoft Docs
description: SQL Server 設定を変更するために、Azure でホストされている SQL Server VM 用の Azure portal 内の SQL 仮想マシン リソースにアクセスする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: pamela
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: 602cb3affc441c92dfa29ac41de893c96a617ff7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301876"
---
# <a name="manage-sql-server-vms-by-using-the-azure-portal"></a>Azure portal を使用して SQL Server VM を管理する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Azure portal](https://portal.azure.com) において、[**SQL 仮想マシン**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースは、SQL Server IaaS Agent 拡張機能に登録された Azure Virtual Machines 上の SQL Server を管理するための独立した管理サービスです。 このリソースを使用すると、すべての SQL Server VM を同時に表示し、SQL Server 専用の設定を変更できます。 

![SQL 仮想マシン リソース](./media/manage-sql-vm-portal/sql-vm-manage.png)

**SQL 仮想マシン** のリソース管理ポイントは、開始、停止、再起動といった VM を管理するために使用される **仮想マシン** リソースとは異なります。 


## <a name="prerequisite"></a>前提条件 

**SQL 仮想マシン** リソースは、[SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)されている SQL Server VM でのみ使用できます。 


## <a name="access-the-resource"></a>リソースにアクセスする

**SQL 仮想マシン** リソースにアクセスするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com)を開きます。 
1. **[すべてのサービス]** を選択します。 
1. 検索ボックスに「**SQL 仮想マシン**」と入力します。
1. (省略可能): **[SQL 仮想マシン]** の横にある星を選択して、このオプションを **[お気に入り]** メニューに追加します。 
1. **[SQL virtual machines]\(SQL 仮想マシン\)** を選択します。 

   ![[すべてのサービス] で SQL Server 仮想マシンを見つける](./media/manage-sql-vm-portal/sql-vm-search.png)

1. このポータルに、サブスクリプション内で使用可能なすべての SQL Server VM が一覧表示されます。 管理したいものを選択して、**SQL 仮想マシン** リソースを開きます。 SQL Server VM が表示されていない場合は、検索ボックスを使用します。 

   ![使用可能なすべての SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   対象の SQL Server VM を選択すると、**SQL 仮想マシン** リソースが開きます。 


   ![SQL 仮想マシン リソースを表示する](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 仮想マシン** リソースは専用の SQL Server 設定用のリソースです。 **[仮想マシン]** ボックスで VM の名前を選択し、その VM (ただし、SQL Server に限定されません) に固有の設定を開きます。 


## <a name="license-and-edition"></a>ライセンスとエディション 

SQL 仮想マシン リソースの **[構成]** ページを使用して、SQL Server ライセンス メタデータを **[従量課金制]** 、 **[Azure ハイブリッド特典]** 、または [ディザスター リカバリー用の無料の Azure レプリカ](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)のための **[HA/DR]** に変更します。



![SQL 仮想マシン リソースを使用して Azure portal 内の SQL Server VM メタデータのバージョンとエディションを変更する](./media/manage-sql-vm-portal/sql-vm-license-edition.png)

**[構成]** ページでは、SQL Server のエディションを **Enterprise**、**Standard**、**Developer** などに変更することもできます。 

Azure portal でのライセンスとエディションのメタデータの変更は、SQL Server のバージョンとエディションが内部で VM に変更された後にのみサポートされます。 詳細については、Azure VM での SQL Server の[バージョン](change-sql-server-version.md)と[エディション](change-sql-server-edition.md)の変更に関する記事を参照してください。 

## <a name="storage"></a>記憶域 

SQL 仮想マシン リソースの **[構成]** ページを使用して、データ、ログ、tempdb ドライブを拡張します。 

![SQL 仮想マシン リソースを使用して Azure portal のストレージを拡張する](./media/manage-sql-vm-portal/sql-vm-storage-configuration.png)

## <a name="patching"></a>Patching

SQL 仮想マシン リソースの **[修正]** ページを使用して、VM の自動修正を有効にし、重要としてマークされている Windows および SQL Server の更新プログラムを自動的にインストールします。 ここでは、毎日の修正の実行や、メンテナンスのローカル開始時刻、メンテナンス期間など、メンテナンス スケジュールを構成することもできます。 


![SQL 仮想マシン リソースを使用して Azure portal で自動修正とスケジュールを構成する](./media/manage-sql-vm-portal/sql-vm-automated-patching.png)


詳細については、[自動修正](automated-patching.md)に関するページを参照してください。 



## <a name="backups"></a>バックアップ

SQL 仮想マシン リソースの **[バックアップ]** ページを使用して、保有期間、使用するストレージ アカウント、暗号化、システム データベースをバックアップするかどうか、バックアップ スケジュールなどの自動バックアップ設定を構成します。 

![SQL 仮想マシン リソースを使用して、Azure portal で自動バックアップとスケジュールを構成する](./media/manage-sql-vm-portal/sql-vm-automated-backup.png)

詳細については、[自動修正](automated-backup.md)に関するページを参照してください。 


## <a name="high-availability-preview"></a>高可用性 (プレビュー)

SQL 仮想マシン リソースの **[高可用性]** ページを使用して、Windows Server フェールオーバー クラスターを作成し、Always On 可用性グループ、可用性グループ リスナー、Azure Load Balancer を構成します。 Azure portal を使用した高可用性の構成は、現在プレビュー段階です。 


![SQL 仮想マシン リソースを使用して、Azure portal で Windows Server フェールオーバー クラスターと Always On 可用性グループを構成する](./media/manage-sql-vm-portal/sql-vm-high-availability.png)


詳細については、[Azure portal を使用した可用性グループの構成](availability-group-azure-portal-configure.md)に関するページを参照してください。

## <a name="security-configuration"></a>セキュリティの構成 

SQL 仮想マシン リソースの **[セキュリティの構成]** ページを使用して、使用するポート、SQL 認証が有効になっているかどうか、Azure Key Vault 統合を有効にするかどうかなどの SQL Server セキュリティ設定を構成します。 

![SQL 仮想マシン リソースを使用して Azure portal の SQL Server セキュリティを構成する](./media/manage-sql-vm-portal/sql-vm-security-configuration.png)

詳細については、[セキュリティのベスト プラクティス](security-considerations-best-practices.md)に関するページをご覧ください。

<a name="security-center"></a>

## <a name="defender-for-cloud"></a>Defender for Cloud 

SQL 仮想マシンのリソースの **[Defender for SQL]** ページを使用して、SQL 仮想マシン ブレードに Defender for Cloud の推奨事項を直接表示します。 この機能を活用するには、[Microsoft Defender for SQL](../../../security-center/defender-for-sql-usage.md) を有効にします。 

![SQL 仮想マシン リソースを使用して Azure portal で SQL Server Defender for Cloud 設定を構成する](./media/manage-sql-vm-portal/sql-vm-security-center.png)

## <a name="sql-assessment-preview"></a>SQL評価 (プレビュー)

SQL 仮想マシンリソースの **[SQL Assessment]** ページを使用して、SQL Server VM の正常性を評価します。 この機能を有効にすると、SQL Server インスタンスとデータベースがスキャンされ、パフォーマンス (インデックス、統計、トレースフラグなど) を向上させ、不足しているベストプラクティス構成を特定するための推奨事項が表示されます。 SQL評価は現在プレビューの段階です。 

:::image type="content" source="./media/manage-sql-vm-portal/sql-vm-assessment-workbook.png" alt-text="[SQL 仮想マシンリソース] ページの Azure portal で SQL 評価を表示します。" lightbox="./media/manage-sql-vm-portal/sql-vm-assessment-workbook.png":::

詳細については、 [Azure vm での SQL Server の SQL Assessment に関する](sql-assessment-for-sql-vm.md)ページを参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.yml)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Azure VM 上の SQL Server の新機能](doc-changes-updates-release-notes-whats-new.md)

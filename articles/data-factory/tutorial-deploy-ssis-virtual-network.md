---
title: 仮想ネットワークに参加するように Azure-SSIS 統合ランタイムを構成するチュートリアル
description: 仮想ネットワークに参加するように Azure-SSIS 統合ランタイムを構成する方法について説明します。
author: swinarko
ms.author: sawinark
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 8ee28e9c310002c0a0b2e29a77b35c71045c9fc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040865"
---
# <a name="configure-azure-ssis-integration-runtime-to-join-a-virtual-network"></a>仮想ネットワークに参加するように Azure-SSIS 統合ランタイムを構成する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure portal/Azure Data Factory (ADF) UI を使用して、Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を構成し、高速挿入方法で仮想ネットワークに参加するように最小限の手順を説明します。  これには次の手順が含まれます。

1. 仮想ネットワークを構成して、高速挿入方法を構成Azure portal。
1. ADF UI Azure-SSIS IRによる高速挿入方法を使用して仮想ネットワークに接続する

## <a name="prerequisites"></a>前提条件

- **Azure-SSIS IR:** まだない場合は、最初に [ADF UI をAzure-SSIS IRを作成](tutorial-deploy-ssis-packages-azure.md) します。

- **仮想ネットワーク**: 仮想ネットワークをまだ作成していない場合は、 [最初に](../virtual-network/quick-create-portal.md) 仮想ネットワークをAzure portalします。 次の情報を確認します。
  - 仮想ネットワークにリソース ロックはありません。
  - 仮想ネットワークまたはサブネットAzure-SSIS IR作成するユーザーには、必要なロールベースのアクセス制御 (RBAC) アクセス許可が付与されます。「仮想ネットワークのアクセス許可の選択」セクションを[参照してください。](azure-ssis-integration-runtime-express-virtual-network-injection.md#perms)

このチュートリアルでは、次の仮想ネットワーク構成については説明しません。

- 静的パブリック IP アドレスを使用する場合は、Azure-SSIS IR。
- 独自のドメイン ネーム システム (DNS) サーバーを使用する。
- ネットワーク セキュリティ グループ (NSG) を使用する。
- ユーザー定義ルート (UDR) を使用する。

これらの省略可能な手順を実行する場合は、Express 仮想ネットワークインジェクション方法に関する [記事を参照](azure-ssis-integration-runtime-express-virtual-network-injection.md) してください。

## <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する

仮想Azure portalネットワークに参加する前に、仮想ネットワークを構成Azure-SSIS IR使用します。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、これらの Web ブラウザーのみが ADF UI をサポートしています。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。

1. 一覧で目的の仮想ネットワークを選びます。

1. 左側のメニューで、 **[サブネット]** を選択します。

   - Azure-SSIS IR を参加させる適切なサブネットがあることを確認します。「[サブネットの選択](azure-ssis-integration-runtime-express-virtual-network-injection.md#subnet)」のセクションを参照してください。

   - 選択したサブネットがグループに委任Azure Batch、サブネットを委任する方法に関する [セクションAzure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet) してください。

1. *Microsoft.Batch* が、Azure-SSIS IR を参加させる仮想ネットワークを持つ Azure サブスクリプションに登録されたリソース プロバイダーであることを確認します。 詳細な手順については、「[リソース プロバイダーとして Azure Batch を登録する](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch)」のセクションを参照してください。

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>仮想ネットワークに Azure-SSIS IR を参加させる

仮想ネットワークを構成した後、仮想ネットワークにAzure-SSIS IR接続できます。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、これらの Web ブラウザーのみが ADF UI をサポートしています。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、 **[その他のサービス]** を選択し、 **[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選択します。

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="データ ファクトリの一覧":::

1. 一覧で Azure-SSIS IR を含む ADF を選択します。 ADF のホーム ページが表示されます。 **[作成と監視]** タイルを選択します。 別のタブに ADF の UI が表示されます。

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="データ ファクトリのホーム ページ":::

1. ADF の UI で、 **[編集]** タブに切り替え、 **[接続]** を選択し、 **[統合ランタイム]** タブに切り替えます。

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="&quot;[統合ランタイム]&quot; タブ":::

1. Azure-SSIS IR が実行されている場合、 **[統合ランタイム]** 一覧の **[アクション]** 列で Azure-SSIS IR の **[停止]** ボタンを選択します。 Azure-SSIS IR は、停止するまで編集できません。

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="IR を停止する":::

1. **[統合ランタイム]** 一覧の **[アクション]** 列で、Azure-SSIS IR の **[編集]** ボタンを選択します。

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="統合ランタイムを編集する":::

1. **[Integration Runtime Setup]\(統合ランタイムのセットアップ\)** パネルで、 **[全般設定]** ページと **[Deployment settings]\(配置の設定\)** ページで **[次へ]** ボタンを選択して先に進みます。

1. **[詳細設定]** ページで、次の手順を行います。

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet-express.png" alt-text="高速仮想ネットワークインジェクションの詳細設定":::

   1. **[Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses]\(参加させる Azure-SSIS 統合ランタイムの VNet を選択し、ADF で特定のネットワーク リソースを作成できるようにし、必要に応じて独自の静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。

   1. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。

   1. **[場所]** では、統合ランタイムと同じ場所が選択されています。

   1. **[タイプ]** で、仮想ネットワークの種類を **Azure Resource Manager Virtual Network** または従来の仮想ネットワークから選択します。 従来の仮想ネットワークは間もなく非推奨になるので、**Azure Resource Manager Virtual Network** を選択することをお勧めします。

   1. **[VNet 名]** で、仮想ネットワークの名前を選択します。 これは、SSISDB をホストする Azure SQL Database サーバーの仮想ネットワーク サービス エンドポイントまたはプライベート エンドポイントの構成に使用されるものと同じものである必要があります。 または、SSISDB をホストする Azure SQL Managed Instance が参加するものと同じものである必要があります。 または、オンプレミス ネットワークに接続されているものと同じである必要があります。

   1. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストする Azure SQL Database サーバーの仮想ネットワーク サービス エンドポイントの構成に使用されるものと同じものである必要があります。 または、SSISDB をホストする Azure SQL Managed Instance が参加するものとは異なるサブネットである必要があります。

   1. **VNet インジェクション方法の場合は、Express** を選択して **高速** 仮想ネットワークインジェクションを行います。

   1. **[VNet Validation]\(VNet の検証\)** を選択します。 検証が成功した場合は、 **[続行]** を選択します。

1. **[Summary]\(概要\)** ページで、Azure-SSIS IR のすべての設定を確認して **[更新]** を選択します。

1. Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンを選択して、Azure-SSIS IR を開始します。 高速挿入方法を使用して仮想ネットワークAzure-SSIS IRを開始するには、約 5 分かかります。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークを構成して Azure-SSIS IR のインジェクションを行う](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [ADF UI を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して仮想ネットワークに Azure-SSIS IR を参加します](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。

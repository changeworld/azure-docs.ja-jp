---
title: UI を使用し、Azure-SSIS Integration Runtime を仮想ネットワークに参加させる
description: Azure Data Factory Studio UI を使用し、Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d02a5d1025e470d8751d87816cbcc6395be41593
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403132"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-data-factory-studio-ui"></a>Azure Data Factory Studio UI を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる

このセクションでは、Azure portal と Azure Data Factory Studio UI を使用し、既存の Azure-SSIS IR を仮想ネットワーク (クラシックまたは Azure Resource Manager) に参加させる方法について説明します。 

Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを適切に構成する必要があります。 仮想ネットワークの種類 (クラシックまたは Azure Resource Manager) に該当するセクションの手順を実行してください。 次に、3 番目のセクションの手順に従って、Azure-SSIS IR を仮想ネットワークに参加させます。 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 仮想ネットワークを構成する

Azure-SSIS IR を参加させる前に、ポータルを使用して Azure Resource Manager 仮想ネットワークを構成します。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク]** ページで、 **[プロパティ]** を選びます。 

1. **[リソース ID]** のコピー ボタンを選び、仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューの **[サブネット]** を選択します。 使用可能なアドレスの数が Azure-SSIS IR のノード数より多いことを確認します。 

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 または、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。) 

   1. Azure portal の左側のメニューで、 **[サブスクリプション]** を選択します。 

   1. サブスクリプションを選択します。 

   1. 左側の **[リソース プロバイダー]** を選択し、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="&quot;登録済み&quot;状態の確認":::

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに [空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

## <a name="configure-a-classic-virtual-network"></a>従来の仮想ネットワークを構成する

Azure-SSIS IR を参加させる前に、ポータルを使用して従来の仮想ネットワークを構成します。 

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 **[仮想ネットワーク (クラシック)]** を選びます。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク (クラシック)]** ページで、 **[プロパティ]** を選びます。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="クラシック仮想ネットワークのリソース ID":::

1. **[リソース ID]** のコピー ボタンを選び、クラシック ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューの **[サブネット]** を選択します。 使用可能なアドレスの数が Azure-SSIS IR のノード数より多いことを確認します。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="仮想ネットワークで使用可能なアドレスの数":::

1. **MicrosoftAzureBatch** を仮想ネットワークの **従来の仮想マシン共同作成者** ロールに参加させます。 

   1. 左側のメニューの **[アクセス制御 (IAM)]** を選択し、 **[ロール割り当て]** タブを選択します。 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;[アクセス制御]&quot; ボタンと &quot;[追加]&quot; ボタン":::

   1. **[ロールの割り当ての追加]** を選択します。

   1. **[ロールの割り当ての追加]** ページで、 **[ロール]** に **[従来の仮想マシン共同作成者]** を選択します。 **[選択]** ボックスに「**ddbf3205-c6bd-46ae-8127-60eb93363864**」を貼り付け、検索結果の一覧から **[Microsoft Azure Batch]** を選択します。 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="&quot;[ロールの割り当ての追加]&quot; ページでの検索結果":::

   1. **[保存]** を選択して設定を保存し、ページを閉じます。 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="アクセス設定の保存":::

   1. 共同作成者の一覧に **Microsoft Azure Batch** があることを確認します。 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Azure Batch アクセスの確認":::

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 または、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。) 

   1. Azure portal の左側のメニューで、 **[サブスクリプション]** を選択します。 

   1. サブスクリプションを選択します。 

   1. 左側の **[リソース プロバイダー]** を選択し、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="&quot;登録済み&quot;状態の確認":::

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに [空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR を仮想ネットワークに参加させる

Azure Resource Manager 仮想ネットワークまたは従来の仮想ネットワークを構成した後は、Azure-SSIS IR を仮想ネットワークに参加させることができます。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、 **[その他のサービス]** を選択し、 **[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選択します。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="データ ファクトリの一覧":::

1. 一覧で Azure-SSIS IR を使用するデータ ファクトリを選択します。 データ ファクトリのホーム ページが表示されます。 **[作成と監視]** タイルを選択します。 Data Factory の UI が別のタブに表示されます。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="データ ファクトリのホーム ページ":::

1. データ ファクトリの UI で、 **[編集]** タブに切り替え、 **[接続]** を選択し、 **[統合ランタイム]** タブに切り替えます。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="&quot;[統合ランタイム]&quot; タブ":::

1. Azure-SSIS IR が実行されている場合、 **[統合ランタイム]** 一覧の **[アクション]** 列で Azure-SSIS IR の **[停止]** ボタンを選択します。 Azure-SSIS IR は、停止するまで編集できません。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="IR を停止する":::

1. **[統合ランタイム]** 一覧の **[アクション]** 列で、Azure-SSIS IR の **[編集]** ボタンを選択します。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="統合ランタイムを編集する":::

1. 統合ランタイムの設定パネルで、 **[全般設定]** と **[SQL 設定]** セクションの **[次へ]** ボタンを選択して、先に進みます。 

1. **[詳細設定]** セクションで、次の手順を実行します。 

   1. **[Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses]\(参加させる Azure-SSIS 統合ランタイムの VNet を選択し、ADF で特定のネットワーク リソースを作成できるようにし、必要に応じて独自の静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。 

   1. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。

   1. **[場所]** では、統合ランタイムと同じ場所が選択されています。

   1. **[種類]** で、仮想ネットワークの種類の (クラシックまたは Azure Resource Manager) を選択します。 クラシック仮想ネットワークは間もなく非推奨になるので、Azure Resource Manager 仮想ネットワークを選択することをお勧めします。

   1. **[VNet 名]** で、仮想ネットワークの名前を選択します。 これは、SSISDB をホストするために仮想ネットワーク サービス エンドポイントを備えた SQL Database またはプライベート エンドポイントを備えた SQL Managed Instance に使用されているものと同じである必要があります。 または、オンプレミス ネットワークに接続されているものと同じである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意の仮想ネットワークを指定できます。

   1. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストするために仮想ネットワーク サービス エンドポイントを備えた SQL Database に使用されているものと同じである必要があります。 または、SSISDB をホストするためにプライベート エンドポイントを備えた SQL Managed Instance に使用されているものとは異なるサブネットである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意のサブネットを指定できます。

   1. Azure-SSIS IR に独自の静的パブリック IP アドレスを使用して、それらをデータ ソースのファイアウォールで許可するかどうかを選択するには、 **[Bring static public IP addresses for your Azure-SSIS Integration Runtime]\(Azure-SSIS 統合ランタイムに静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。

      このチェック ボックスを選択した場合、次の手順を行います。

      1. **[First static public IP address]\(最初の静的パブリック IP アドレス\)** では、Azure-SSIS IR の [要件を満たす](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP)最初の静的パブリック IP アドレスを選択します。 まだお持ちでない場合は、 **[新規作成]** リンクをクリックして Azure portal で静的パブリック IP アドレスを作成してから、ここにある更新ボタンをクリックして、それを選択できるようにします。
      
      1. **[Second static public IP address]\(2 番目の静的パブリック IP アドレス\)** では、Azure-SSIS IR の [要件を満たす](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP) 2 番目の静的パブリック IP アドレスを選択します。 まだお持ちでない場合は、 **[新規作成]** リンクをクリックして Azure portal で静的パブリック IP アドレスを作成してから、ここにある更新ボタンをクリックして、それを選択できるようにします。

   1. **[VNet Validation]\(VNet の検証\)** を選択します。 検証が成功した場合は、 **[続行]** を選択します。 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="仮想ネットワークの詳細設定":::

1. **[概要]** セクションで、Azure-SSIS IR のすべての設定を確認します。 次に、 **[更新]** を選択します。

1. Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンを選択して、Azure-SSIS IR を開始します。 仮想ネットワークに参加する Azure-SSIS IR が開始されるまでに約 20 から 30 分かかります。 

## <a name="next-steps"></a>次のステップ
- [Azure-SSIS Integration Runtime を仮想ネットワークに参加させる - 概要](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure-SSIS Integration Runtime 仮想ネットワーク構成の詳細](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 
- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](./tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 Azure SQL Database を使って SSIS カタログをホストします。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイントまたは仮想ネットワーク内の SQL Managed Instance を含む Azure SQL Database を使用して、SSIS カタログをホストする手順について説明されます。 Azure-SSIS IR を仮想ネットワークに参加させる方法が示されます。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法が説明されています。 返された情報の状態の説明が提供されます。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。

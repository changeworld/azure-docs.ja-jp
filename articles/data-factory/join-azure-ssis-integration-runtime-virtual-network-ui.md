---
title: Azure portal を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
description: Azure portal を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15b2a1bbac1d89c74a2fdea2f5ce3af51261b29e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087896"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-portal"></a>Azure portal を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる

この記事では、Azure portal/ADF UI を使用して、Azure Data Factory (ADF) の既存の Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を仮想ネットワークに参加させる方法について説明します。 

Azure-SSIS IR を仮想ネットワークに参加させる前に、まず仮想ネットワークを適切に構成しておく必要があります。 「[仮想ネットワークを構成して Azure-SSIS IR のインジェクションを行う](azure-ssis-integration-runtime-virtual-network-configuration.md)」の記事を参照してください。 次に、仮想ネットワークの種類 (Azure Resource Manager またはクラシック) に該当する以下のセクションの手順を実行します。 最後に、最後のセクションの手順に従って、Azure-SSIS IR を仮想ネットワークに参加させます。 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 仮想ネットワークを構成する

Azure-SSIS IR を参加させる前に、Azure portal を使用して Azure Resource Manager 仮想ネットワークを構成します。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、これらの Web ブラウザーのみが ADF UI をサポートしています。 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. 左側のメニューで、 **[サブネット]** を選択します。

   - Azure-SSIS IR を参加させる適切なサブネットがあることを確認します。「[サブネットの選択](azure-ssis-integration-runtime-standard-virtual-network-injection.md#subnet)」のセクションを参照してください。

   - 高速仮想ネットワーク インジェクションの方法を使用する場合は、選択したサブネットが Azure Batch に委任されていることを確認します。「[サブネットを Azure Batch に委任する](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet)」のセクションを参照してください。

1. *Microsoft.Batch* が、Azure-SSIS IR を参加させる仮想ネットワークを持つ Azure サブスクリプションの登録されたリソース プロバイダーであることを確認します。 詳細な手順については、「[リソース プロバイダーとして Azure Batch を登録する](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch)」のセクションを参照してください。

## <a name="configure-a-classic-virtual-network"></a>従来の仮想ネットワークを構成する

Azure-SSIS IR を参加させる前に、Azure portal を使用して従来の仮想ネットワークを構成します。 

1. Microsoft Edge または Google Chrome を起動します。 現時点では、これらの Web ブラウザーのみが ADF UI をサポートしています。 

1. [Azure portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 **[仮想ネットワーク (クラシック)]** を選びます。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク (クラシック)]** ページで、 **[プロパティ]** を選びます。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="クラシック仮想ネットワークのリソース ID":::

1. **[リソース ID]** のコピー ボタンを選び、従来の仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューで、 **[サブネット]** を選択します。 選択したサブネット内で使用可能な IP アドレスの数が、Azure-SSIS IR ノードの数の 2 倍を超えていることを確認します。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="仮想ネットワークで使用可能なアドレスの数":::

1. **MicrosoftAzureBatch** を仮想ネットワークの **従来の仮想マシン共同作成者** ロールに参加させます。 

   1. 左側のメニューの **[アクセス制御 (IAM)]** を選択し、 **[ロール割り当て]** タブを選択します。 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;[アクセス制御]&quot; ボタンと &quot;[追加]&quot; ボタン":::

   1. **[ロールの割り当ての追加]** を選択します。

   1. **[ロールの割り当ての追加]** ページで、 **[ロール]** に **[従来の仮想マシン共同作成者]** を選択します。 **[選択]** ボックスに「**ddbf3205-c6bd-46ae-8127-60eb93363864**」を貼り付け、検索結果の一覧から **[Microsoft Azure Batch]** を選択します。 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="&quot;[ロールの割り当ての追加]&quot; ページでの検索結果":::

   1. **[保存]** を選択して設定を保存し、ページを閉じます。 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="アクセス設定の保存":::

   1. 共同作成者の一覧に **MicrosoftAzureBatch** があることを確認します。 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Azure Batch アクセスの確認":::

1. *Microsoft.Batch* が、Azure-SSIS IR を参加させる仮想ネットワークを持つ Azure サブスクリプションの登録されたリソース プロバイダーであることを確認します。 詳細な手順については、「[リソース プロバイダーとして Azure Batch を登録する](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch)」のセクションを参照してください。

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>仮想ネットワークに Azure-SSIS IR を参加させる

Azure Resource Manager または従来の仮想ネットワークを構成した後は、Azure-SSIS IR を仮想ネットワークに参加させることができます。

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

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="仮想ネットワークの詳細設定":::

   1. **[Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses]\(参加させる Azure-SSIS 統合ランタイムの VNet を選択し、ADF で特定のネットワーク リソースを作成できるようにし、必要に応じて独自の静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。 

   1. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。

   1. **[場所]** では、統合ランタイムと同じ場所が選択されています。

   1. **[タイプ]** で、仮想ネットワークの種類を **Azure Resource Manager Virtual Network** または従来の仮想ネットワークから選択します。 従来の仮想ネットワークは間もなく非推奨になるので、**Azure Resource Manager 仮想ネットワーク** を選択することをお勧めします。

   1. **[VNet 名]** で、仮想ネットワークの名前を選択します。 これは、SSISDB をホストする Azure SQL Database サーバーの仮想ネットワーク サービス エンドポイントまたはプライベート エンドポイントの構成に使用されるものと同じものである必要があります。 または、SSISDB をホストする Azure SQL Managed Instance が参加するものと同じものである必要があります。 または、オンプレミス ネットワークに接続されているものと同じである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意の仮想ネットワークを指定できます。

   1. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストする Azure SQL Database サーバーの仮想ネットワーク サービス エンドポイントの構成に使用されるものと同じものである必要があります。 または、SSISDB をホストする Azure SQL Managed Instance が参加するものとは異なるサブネットである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意のサブネットを指定できます。

   1. **VNet インジェクション方法** の場合は、仮想ネットワーク インジェクションの方法として **[Express]\(高速\)** / **[Standard]\(標準\)** を選択します。 
   
      これらの方法を比較するには、「[標準と高速の仮想ネットワークの挿入方法の比較](azure-ssis-integration-runtime-virtual-network-configuration.md#compare)」に関する記事を参照してください。 
   
      **[Express]\(高速\)** を選択した場合は、「[高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)」の記事を参照してください。 
      
      **[Standard]\(標準\)** を選択した場合は、「[標準仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md)」の記事を参照してください。  この方法を使用すると、次の処理を実行できます。

      1. Azure-SSIS IR に独自の静的パブリック IP アドレスを使用して、それらをデータ ストアのファイアウォールで許可するかどうかを選択するには、 **[Bring static public IP addresses for your Azure-SSIS Integration Runtime]\(Azure-SSIS 統合ランタイムに静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。

         このチェック ボックスを選択した場合、次の手順を行います。

         1. **[First static public IP address]\(最初の静的パブリック IP アドレス\)** では、Azure-SSIS IR の [要件を満たす](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip)最初の静的パブリック IP アドレスを選択します。 まだお持ちでない場合は、 **[新規作成]** リンクをクリックして Azure portal で静的パブリック IP アドレスを作成してから、ここにある更新ボタンをクリックして、それを選択できるようにします。
      
         1. **[Second static public IP address]\(2 番目の静的パブリック IP アドレス\)** では、Azure-SSIS IR の [要件を満たす](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) 2 番目の静的パブリック IP アドレスを選択します。 まだお持ちでない場合は、 **[新規作成]** リンクをクリックして Azure portal で静的パブリック IP アドレスを作成してから、ここにある更新ボタンをクリックして、それを選択できるようにします。

   1. **[VNet Validation]\(VNet の検証\)** を選択します。 検証が成功した場合は、 **[続行]** を選択します。 

1. **[Summary]\(概要\)** ページで、Azure-SSIS IR のすべての設定を確認して **[更新]** を選択します。

1. Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンを選択して、Azure-SSIS IR を開始します。 高速または標準のインジェクション方法を使用して、仮想ネットワークに参加する Azure-SSIS IR を開始するには、それぞれ約 5 分または 20 から 30 分かかります。 

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークを構成して Azure-SSIS IR のインジェクションを行う](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [標準仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Azure PowerShell を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。

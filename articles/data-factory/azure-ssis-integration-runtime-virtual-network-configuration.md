---
title: Azure-SSIS統合ランタイムのインジェクションするための仮想ネットワークの設定
description: Azure SSIS 統合ランタイムのインジェクションに仮想ネットワークを設定する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 11/03/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e3683011794e89acbac6bbbb985c7cb85ebd9ebd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475990"
---
# <a name="configure-a-virtual-network-for-injection-of-azure-ssis-integration-runtime"></a>Azure-SSIS統合ランタイムのインジェクションするための仮想ネットワークの設定

Azure Data Factory (ADF) で SQL Server Integration Services (SSIS) を使用する場合、Azure-SSIS 統合ランタイム (IR) を仮想ネットワークに結合させる2 つの方法 (標準と高速) があります。 高速方法は、Azure-SSIS IRをより早く開始し、インバウンドやアウトバウンドのトラフィック要件もありませんが、標準方法と比較していくつかの制限があります。

## <a name="compare-the-standard-and-express-virtual-network-injection-methods"></a><a name="compare"></a>標準および高速仮想ネットワークのインジェクション方法を比較する

ここでは、標準仮想ネットワークインジェクション方法と高速仮想ネットワークインジェクション方法の違いを表にまとめています。

| 比較 | 標準仮想ネットワーク インジェクション | 高速仮想ネットワーク インジェクション |
|------------|------------------------------------|-----------------------------------|
| **Azure-SSIS IR 開始期間** | 約30分。 | 約5分です。 | 
| **Azure サブスクリプション & リソースグループの設定** | *Microsoft Batch* は、仮想ネットワークサブスクリプションでリソースプロバイダーとして登録されている必要があります。<br/><br/>仮想ネットワークリソースグループでは、パブリック IP アドレス、ロードバランサー、およびネットワークセキュリティグループ (NSG) の作成が許可されている必要があります。 | *Microsoft Batch* は、仮想ネットワークサブスクリプションでリソースプロバイダーとして登録されている必要があります。 | 
| **仮想ネットワーク サブネット** | サブネットは、他のAzureサービスには専用されません。 | サブネットは、他のAzureサービスには専用されません。<br/><br/>サブネットは、 *Microsoft Batch/batchAccounts* に委任されている必要があります。 | 
| **仮想ネットワークのアクセス許可** | Azure-SSIS IR を作成するユーザーは、 _Microsoft. Network/virtualNetworks/\*/ join_ アクセス許可を持っている必要があります。 | Azure-SSIS IR を作成するユーザーは、 *Microsoft.Network/virtualNetworks/subnets/join/action* のアクセス許可を持っている必要があります。 | 
| **静的パブリック IP アドレス** | （オプション）Azure-SSIS IR用に独自の静的パブリックIPアドレス（BYOIP）を持参してください。 | (オプション) 仮想ネットワークのネットワークアドレス変換 (NAT) を構成して、Azure-SSIS IR の静的パブリック IP アドレスを設定します。 | 
| **カスタム DNS サーバー** | 未解決の DNS 要求を Azure 再帰的リゾルバに転送することをお勧めします。 | 未解決の DNS 要求を Azure 再帰的リゾルバに転送することをお勧めします。<br/><br/>Azure-SSIS IR には、標準カスタムセットアップが必要です。 | 
| **受信トラフィック** | 同期元として *Batchnodemanagement* サービスタグを使用する場合は、ポート *29876、29877* が TCP トラフィック用に開かれている必要があります。 | 不要。 | 
| **送信トラフィック** | 宛先として *Azurecloud* サービスタグを使用して TCP トラフィック用にポート *443* を開く必要があります。 | 宛先として *DataFactoryManagement* サービスタグを使用して TCP トラフィック用にポート *443* を開く必要があります。 | 
| **リソース ロック** | リソースグループでは許可されていません。 | 仮想ネットワークでは許可されていません。 | 
| **仮想ネットワークごとの Azure-SSIS IRs** | 無制限。 | 1つだけです。 | 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png" alt-text="仮想ネットワークの標準および高速インジェクション方法のスクリーンショット" lightbox="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png":::

仮想ネットワークの設定は、インジェクション方法によって異なります。 高速方法を使用する場合は、 [高速仮想ネットワークのインジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md) に関する記事を参照してください。それ以外の場合は、 [標準仮想ネットワークインジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md) に関する記事を参照してください。
  
## <a name="register-azure-batch-as-a-resource-provider"></a><a name="registerbatch"></a>リソースプロバイダーとして Azure Batch を登録する

Azure portalを使って、ADFのSSISの基盤となるAzure Batchを、Azure-SSIS IRが参加する仮想ネットワークを持つAzureサブスクリプションのリソース・プロバイダーとして登録することができます。 既にサブスクリプションで、Azure Batchを使用したり、ADF UIでAzure-SSIS IRを作成している場合は、登録されています。 それ以外の場合は、次の手順を実行します。

1. Azure portal で仮想ネットワークを選択したら、 **[概要]** ページで強調表示されているサブスクリプションを選択します。  

1. 左側のメニューで **[リソースプロバイダー]** を選択します。

1. *Microsoft.Batch* が登録されていない場合は、選択して登録します。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="&quot;登録済み&quot;状態の確認":::

一覧に [ *Microsoft.Batch* ] が表示されない場合は、サブスクリプションに [空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md) し、後で削除することができます。 

## <a name="delegate-a-subnet-to-azure-batch"></a><a name="delegatesubnet"></a>サブネットを Azure Batch に委任する

高速仮想ネットワークのインジェクションを使用する場合は、Azure-SSIS IR がインジェクションされるサブネットを、ADF の SSIS の基盤となるインフラストラクチャである Azure Batch に委任する必要があります。 Azure portal を使用すると、次の手順に従って実行できます。

1. Azure portal で仮想ネットワークを選択した後、左側のメニューで **[サブネット]** を選択します。

1. サブネット名を選択してウィンドウを開き、Azure-SSIS IR ノード番号の2倍以上の使用可能な IP アドレスがあることを確認します。

1. **[サービスへのサブネットの委任]** ドロップダウンメニューで、[*Microsoft.Batch/batchAccounts*] を選択します。

1. **[保存]** ボタンを選択します。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/delegate-subnet-to-batch.png" alt-text="サブネットを Azure Batch に委任する":::

## <a name="grant-virtual-network-permissions"></a><a name="grantperms"></a>仮想ネットワークのアクセス許可を付与する

Azure portal を使用すると、仮想ネットワーク/サブネットに参加するために必要なロールベースのアクセス制御 (RBAC) アクセス許可を作成 Azure-SSIS IR ユーザーに付与することができます。 そのためには、次の手順を実行します。

1. Azure portal で仮想ネットワークを選択した後、標準の仮想ネットワークインジェクションを使用する場合は、左側のメニューで **[アクセス制御 (IAM)]** を選択できます。 高速仮想ネットワークインジェクションを使用する場合は、左側のメニューで **[サブネット]** を選択し、サブネット 行を選択してから、上部メニューの **[ユーザーの管理]** を選択して **[アクセスの制御]** ページを開きます。

1. **[ロールの割り当ての追加]** ボタンをクリックして、 **[ロールの割り当ての追加]** ページを開きます。

1. **Role** リストで *ネットワーク共同作成者* またはカスタムロールを選択し、**Next** ボタンを選択します。

1. **[アクセスの割り当て先]** セクションで、 **[ユーザー、グループ、またはサービスプリンシパル]** オプションを選択します。

1. **[メンバーの選択]** リンクを選択して検索し、Azure-SSIS IR を作成するユーザーを選択します。

1. **[選択]** 、 **[次へ]** 、 **[レビュー + 割り当て]** の各ボタンを選択します。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/grant-virtual-network-permissions.png" alt-text="仮想ネットワークのアクセス許可を付与する":::

## <a name="next-steps"></a>次の手順

- [高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [標準仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して仮想ネットワークに Azure-SSIS IR を参加します](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。

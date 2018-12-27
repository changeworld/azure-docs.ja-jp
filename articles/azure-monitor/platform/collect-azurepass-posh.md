---
title: Log Analytics を使用して Azure PaaS リソース メトリックを収集する | Microsoft Docs
description: Log Analytics で保持して分析するために PowerShell を使用して Azure PaaS リソース メトリックの収集を可能にする方法について説明します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: magoedte
ms.openlocfilehash: b7896ccc979d7c5bcdf6c46cbbef01d261a3a625
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183898"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Log Analytics を使用した Azure PaaS リソース メトリックの収集を構成する

Azure PaaS (サービスとしてのプラットフォーム) リソースは、Azure SQL や Web サイト (Web Apps) 同様、パフォーマンス メトリック データをネイティブに Log Analytics に出力します。 このスクリプトを使用して、特定のリソース グループまたはサブスクリプション全体に既にデプロイされている PaaS リソースのメトリック ログ記録を有効にすることができます。 

現時点では、Azure ポータル経由で PaaS リソースのメトリック ログ記録を有効にする方法はありません。 そのため、PowerShell スクリプトを使用する必要があります。 このネイティブなメトリック ログ記録機能と Log Analytics の監視を使用して、大きな規模で Azure リソースを監視できます。 

## <a name="prerequisites"></a>前提条件
先に進む前に、コンピューターに次の Azure Resource Manager モジュールがインストールされていることを確認します。

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>PowerShell から Azure Resource Manager のコマンドを実行するときの互換性を確保するために、すべての Azure Resource Manager モジュールを同じバージョンにすることをお勧めします。
>
Azure Resource Manager モジュールの最新バージョンをコンピューターにインストールするには、[Azure PowerShell のインストールと構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps)に関する記事を参照してください。  

## <a name="enable-azure-diagnostics"></a>Azure 診断を有効にする  
PaaS リソース用の Azure 診断の構成は、**Enable-AzureRMDiagnostics.ps1** スクリプトを実行することによって実現されます。このスクリプトは [PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52)から入手できます。  スクリプトは、次のシナリオをサポートします。
  
* サブスクリプション内の 1 つまたは複数のリソース グループに関連するリソースを指定する  
* サブスクリプション内の特定のリソース グループに関連するリソースを指定する  
* 別のワークスペースに転送するようにリソースを再構成する

Azure 診断によるメトリックの収集をサポートし、Log Analytics に直接送信するリソースのみがサポートされます。  詳細な一覧については、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](collect-azure-metrics-logs.md)」を参照してください。 

スクリプトをダウンロードして実行するには、次の手順を実行します。

1.  Windows のスタート画面で、「**PowerShell**」と入力し、検索結果から PowerShell を右クリックします。  メニューから **[管理者として実行]** を選択します。   
2. スクリプトを保存するパスを指定して次のコマンドを実行することで、**Enable-AzureRMDiagnostics.ps1** スクリプト ファイルをローカルに保存します。    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. `Connect-AzureRmAccount` を実行して、Azure との接続を作成します。   
4. 次の `.\Enable-AzureRmDiagnostics.ps1` スクリプトを実行します。パラメーターを使用せずに実行すると、サブスクリプション内の特定のリソースからのデータ収集を有効にできます。`-ResourceGroup <myResourceGroup>` パラメーターを使用して、特定のリソース グループ内のリソースを指定できます。   
5. 複数のサブスクリプションがある場合は、適切な値を入力することで、一覧から適切なサブスクリプションを選択します。<br><br> ![スクリプトによって返されるサブスクリプションを選択する](./media/collect-azurepass-posh/script-select-subscription.png)<br> それ以外の場合は、使用可能な単一のサブスクリプションが自動的に選択されます。
6. 次に、スクリプトは、サブスクリプションに登録されている Log Analytics ワークスペースの一覧を返します。  一覧から適切なものを選択します。<br><br> ![スクリプトによって返されるワークスペースを選択する](./media/collect-azurepass-posh/script-select-workspace.png)<br> 
7. 収集を有効にする Azure リソースを選択します。 たとえば、5 を入力すると、SQL Azure データベースに対するデータ収集が有効になります。<br><br> ![スクリプトによって返されたリソースの種類を選択する](./media/collect-azurepass-posh/script-select-resource.png)<br>
   Azure 診断によるメトリックの収集をサポートし、Log Analytics に直接送信するリソースのみを選択できます。  スクリプトは、サブスクリプションまたは指定されたリソース グループで検出したリソースの一覧の **[メトリック]** 列に、値 **[True]** を表示します。    
8. 選択の確認を求めるメッセージが表示されます。  「**Y**」を入力して、選択したすべてのリソースの定義済みの範囲のメトリックのログ記録を有効にします。この記事の例では、サブスクリプション内のすべての SQL データベースが対象です。  

スクリプトは、選択された条件と一致するすべてのリソースに対して実行され、それらのメトリック収集を有効にします。 この操作が終了すると、構成が完了したことを示すメッセージが表示されます。  

完了直後に、Log Analytics リポジトリ内の Azure PaaS リソースからのデータの表示が開始されます。  `AzureMetrics` 型のレコードが作成されます。これらのレコードの分析は、[Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) と [Azure Web Apps Analytics](../../azure-monitor/insights/azure-web-apps-analytics.md) 管理ソリューションによってサポートされます。   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>別のワークスペースにデータを送信するようにリソースを更新する
Log Analytics ワークスペースに既にデータを送信しているリソースがあるときに、別のワークスペースを参照するようにそのリソースを再構成する場合は、`-Update` パラメーターを指定してスクリプトを実行できます。  

**例:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

初回の構成を実行するためにスクリプトを実行したときと同じ情報を提供することを求められます。  

## <a name="next-steps"></a>次の手順

* [ログ検索](../../azure-monitor/log-query/log-query-overview.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。 

* [カスタム フィールド](../../azure-monitor/platform/custom-fields.md)を使用して、イベント レコードを個別のフィールドに解析します。

* 「[Log Analytics で使用するカスタム ダッシュボードの作成](../../azure-monitor/platform/dashboards.md)」で、組織にとって意味のある方法でログ検索を視覚化する方法を理解します。

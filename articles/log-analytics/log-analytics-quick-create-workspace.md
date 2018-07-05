---
title: Azure Log Analytics でワークスペースを作成する | Microsoft Docs
description: Log Analytics ワークスペースを作成して管理ソリューションを有効にし、クラウド環境とオンプレミス環境からデータを収集できるようにします。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 04/02/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: eff79e8ccf0b878b4c083753f245aae993be72de
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127569"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Azure ポータルで Log Analytics ワークスペースを作成する
Azure Portal で Log Analytics ワークスペースを設定できます。このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた Log Analytics 固有の環境です。  次のソースからデータを収集する場合は、この記事で説明する手順を実行する必要があります。

* サブスクリプション内の Azure リソース
* System Center Operations Manager によって監視されているオンプレミスのコンピューター
* System Center Configuration Manager のデバイス コレクション 
* Azure ストレージからの診断またはログ データ

環境内の Azure VM、Windows コンピューター、Linux コンピューターなどの他のソースについては、次のトピックを参照してください。

*  [Azure Virtual Machines に関するデータの収集](log-analytics-quick-collect-azurevm.md) 
*  [Linux コンピューターに関するデータの収集](log-analytics-quick-collect-linux-computer.md)
*  [Windows コンピューターに関するデータの収集](log-analytics-quick-collect-windows-computer.md)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure-portal"></a>Azure Portal へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。 

## <a name="create-a-workspace"></a>ワークスペースの作成
1. Azure Portal で、**[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. **[作成]** をクリックし、次の項目について選択します。

  * 新しい **OMS ワークスペース**の名前 (*DefaultLAWorkspace* など) を指定します。 
  * 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
  * **[リソース グループ]** で、設定済みの既存のリソース グループを使用することを選択するか、新しいリソース グループを作成します。  
  * 使用可能な **[場所]** を選択します。  詳細については、[Log Analytics を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照してください。
  * 2018 年 4 月 2 日より後に作成された新しいサブスクリプションでワークスペースを作成した場合は、自動的に "*1 GB あたり*" の価格プランが使用され、価格レベルを選択するためのオプションは利用できなくなります。  4 月 2 日より前に作成された既存のサブスクリプションのワークスペースを作成している場合、または既存の EA 登録に関連付けられたサブスクリプションに対してワークスペースを作成している場合は、希望の価格レベルを選択します。  特定のレベルの詳細については、[Log Analytics の価格の詳細](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページを参照してください。

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. **[OMS ワークスペース]** ウィンドウに必要な情報を入力したら、**[OK]** をクリックします。  

情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。 

## <a name="next-steps"></a>次の手順
使用できるワークスペースが用意されたので、管理テレメトリの収集の構成、ログ検索の実行によるデータの分析、管理ソリューションの追加による追加データと分析的な考察の提供を行うことができます。 

* Microsoft Azure 診断または Azure ストレージを使用して Azure リソースからデータを収集できるようにするには、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](log-analytics-azure-storage.md)」を参照してください。  
* Operations Manager management グループに報告するエージェントからデータを収集して Log Analytics ワークスペースのリポジトリに格納するには、[データ ソースとして System Center Operations Manager を追加](log-analytics-om-agents.md)します。 
* 階層内のコレクションのメンバーであるコンピュータをインポートするには、[構成マネージャー](log-analytics-sccm.md)に接続します。  
* 使用可能な[管理ソリューション](/log-analytics-add-solutions.md)と、ソリューションをワークスペースに対して追加または削除する方法を確認します。


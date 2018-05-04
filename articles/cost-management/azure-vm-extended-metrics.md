---
title: Azure 仮想マシン用の拡張メトリックを追加する | Microsoft Docs
description: この記事では、Azure VM 用の拡張診断メトリックを有効にして構成する方法を説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4b00baba44a4724ce8f6a45a80692f7f566a35ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Azure 仮想マシン用の拡張メトリックを追加する

Cost Management は、Azure VM からの Azure メトリック データを使って、そのリソースに関する詳細情報を表示します。 メトリック データ (パフォーマンス カウンターとも呼ばれます) は、レポートを生成するために Cost Management によって使われます。 ただし、Cost Management はゲスト VM からのすべての Azure メトリック データの収集を自動的には行いません。ユーザーがメトリックの収集を有効にする必要があります。 この記事では、Azure VM 用の追加診断メトリックを有効にして構成する方法を説明します。

メトリックの収集を有効にした後は、次のことができます。

- VM がメモリ、ディスク、CPU の制限にいつ達するかを把握します。
- 使用状況の傾向と異常を検出します。
- 使用状況に応じてサイズを調整し、コストを管理します。
- Cost Management からコスト効率のよいサイズ設定の最適化に関する推奨事項を取得します。

たとえば、Azure VM の CPU 使用率とメモリ使用率を監視することができます。 Azure VM のメトリックは、_[Host] Percentage CPU_ および _[Guest] Memory percentage_ に対応します。

## <a name="verify-that-metrics-are-enabled-on-vms"></a>VM でメトリックが有効になっていることを確認する

1. Azure Portal (http://portal.azure.com) にログインします。
2. **[仮想マシン]** で VM を選んでから、**[監視]** で **[メトリック]** を選びます。 使用可能なメトリックの一覧が表示されます。
3. メトリックをいくつか選ぶと、グラフにそれらのデータが表示されます。  
    ![メトリックの例: ホストの CPU 使用率](./media/azure-vm-extended-metrics/metric01.png)

上の例では、ホストに関しては限られた一連の標準的なメトリックを表示できますが、メモリのメトリックは表示できません。 メモリ メトリックは拡張メトリックの一部です。 拡張メトリックを有効にするには、さらにいくつかの手順を実行する必要があります。 以下では、拡張メトリックを有効にする手順を説明します。

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Azure Portal で拡張メトリックを有効にする

標準的なメトリックは、ホスト コンピューターのメトリックです。 _[Host] Percentage CPU_ メトリックは 1 つの例です。 ゲスト VM にも基本的なメトリックがあり、それらは拡張メトリックとも呼ばれます。 拡張メトリックの例としては、_[Guest] Memory percentage_ や _[Guest] Memory available_ などがあります。

拡張メトリックを有効にするのは簡単です。 VM ごとに、ゲスト レベルの監視を有効にします。 ゲスト レベルの監視を有効にすると、Azure 診断エージェントが VM にインストールされます。 以降のプロセスは、クラシック VM と通常の VM、および Windows VM と Linux VM で同じです。

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>既存の VM でゲスト レベルの監視を有効にする

1. **[仮想マシン]** で、VM の一覧を表示して、VM を選びます。
2. **[監視]** で **[メトリック]** を選びます。
3. **[診断設定]** をクリックします。
4. [診断設定] ページで、**[ゲスト レベルの監視を有効にする]** をクリックします。 Linux VM では、既存のストレージ アカウントが必要です。 Windows VM でストレージ アカウントを選ばないと、自動的に作成されます。  
    ![ゲスト レベルの監視を有効にする](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. 数分で、VM に Azure 診断エージェントがインストールされます。 ページを最新の情報に更新すると、使用可能なメトリックの一覧がゲスト メトリックで更新されます。  
    ![拡張メトリック](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>新しい VM でゲスト レベルの監視を有効にする

新しい VM を作成するときに、**[ゲスト OS の診断]** を選びます。 Linux VM では、既存のストレージ アカウントが必要です。 Windows VM でストレージ アカウントを選ばないと、自動的に作成されます。

![ゲスト OS の診断を有効にする](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Resource Manager の資格情報

拡張メトリックを有効にした後は、Cost Management が [Resource Manager の資格情報](activate-subs-accounts.md)にアクセスできることを確認します。 Cost Management が VM のパフォーマンス データを収集して表示するには、資格情報が必要です。 資格情報は、コスト最適化の推奨事項の作成にも使われます。 Cost Management は、インスタンスがダウンサイズの推奨の候補かどうかを判定するために、少なくとも 3 日分のパフォーマンス データをインスタンスから収集する必要があります。

## <a name="enable-vm-metrics-with-a-script"></a>スクリプトで VM のメトリックを有効にする

Azure PowerShell スクリプトを使って、VM のメトリックを有効にすることができます。 メトリックを有効にする VM の数が多い場合は、スクリプトを使ってプロセスを自動化できます。 スクリプトの例は、GitHub の「[Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics)」(Azure で診断を有効にする) にあります。

## <a name="view-azure-performance-metrics"></a>Azure のパフォーマンス メトリックを表示する

Cloudyn ポータルで Azure インスタンスのパフォーマンス メトリックを表示するには、**[Assets]\(アセット\)** > **[Compute]\(コンピューティング\)** > **[Instance Explorer]\(インスタンス エクスプローラー\)** に移動します。 VM インスタンスの一覧でインスタンスを展開し、詳細を表示するリソースを展開します。

![インスタンス エクスプローラー](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>次の手順

- アカウントの Azure Resource Manager API アクセスをまだ有効にしていない場合は、「[Azure サブスクリプションとアカウントのアクティブ化](activate-subs-accounts.md)」に進んでください

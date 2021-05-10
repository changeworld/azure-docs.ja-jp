---
title: Start/Stop VMs v2 (プレビュー) のデプロイ
description: この記事では、Azure サブスクリプションで Azure VM の Start/Stop VMs v2 (プレビュー) 機能をデプロイする方法について説明します。
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111294"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Start/Stop VMs v2 (プレビュー) のデプロイ

Start/Stop VMs v2 (プレビュー) 機能をインストールするには、このトピックの手順を順番に実行します。 セットアップ プロセスが完了したら、要件に合わせてカスタマイズするスケジュールを構成します。

## <a name="deploy-feature"></a>機能をデプロイする

デプロイは、[こちらの](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md) Start/Stop VMs v2 の GitHub 組織から開始されます。 この機能は、サブスクリプション内のすべてのリソース グループにわたるすべての VM を、そのサブスクリプション内の 1 つのデプロイから管理することを目的としていますが、組織の運用モデルまたは要件に基づいて、別のインスタンスをインストールすることもできます。 また、複数のサブスクリプション全体で VM を一元的に管理するように構成することもできます。

管理と削除を簡単にするために、Start/Stop VMs v2 (プレビュー) を専用のリソース グループにデプロイすることをお勧めします。

> [!NOTE]
> 現在、このプレビューでは、既存のストレージ アカウントまたは Application Insights リソースの指定はサポートされていません。

1. ブラウザーを開いて、Start/Stop VMs v2 の [GitHub 組織](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)に移動します。
1. お使いの Azure VM が作成されている Azure クラウド環境に基づいて、デプロイ オプションを選択します。 これにより、Azure portal にカスタムの Azure Resource Manager のデプロイ ページが開きます。
1. メッセージが表示されたら、[Azure portal](https://portal.azure.com) にサインインします。
1. 次の値を入力します。

    |名前 |値 |
    |-----|------|
    |リージョン |新しいリソース用にご自身に近いリージョンを選択します。|
    |リソース グループ名 |Start/Stop VMs 用の個々のリソースが含まれるリソース グループの名前を指定します。 |
    |Resource Group Region (リソース グループのリージョン) |リソース グループのリージョンを選択します。 たとえば **[米国中部]** です。|
    |Azure Function App Name (Azure Function App 名) |URL パスに有効な名前を入力します。 入力した名前は、Azure Functions 内での一意性を確保するために検証されます。 |
    |Application Insights Name (Application Insights 名) |Start/Stop VMs の分析を保持する Application Insights インスタンスの名前を指定します。 |
    |Application Insights Region (Application Insights のリージョン) |Application Insights インスタンスのリージョンを指定します。|
    |ストレージ アカウント名 |Start/Stop VMs の実行テレメトリを格納する Azure Storage アカウントの名前を指定します。 |
    |電子メール アドレス |状態の通知を受信する 1 つ以上の電子メール アドレスをコンマ (,) で区切って指定します。|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Start/Stop VMs テンプレートのデプロイ構成":::

1. ページ下部の **[確認および作成]** を選択します。
1. **[作成]** を選択してデプロイを開始します。
1. 画面の上部にあるベルのアイコン (通知) を選択して、デプロイの状態を確認します。 "**デプロイを実行しています**" と表示されます。 デプロイが完了するまでお待ちください。
1. 通知ウィンドウで **[リソース グループに移動]** を選択します。 次のような画面が表示されます。

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Start/Stop VMs テンプレートのデプロイ リソース一覧":::

## <a name="enable-multiple-subscriptions"></a>複数のサブスクリプションを有効にする

Start/Stop のデプロイが完了したら、次の手順を実行して、Start/Stop VMs v2 (プレビュー) で複数のサブスクリプションに対してアクションを実行できるようにします。

1. デプロイ中に指定した Azure Function App 名の値をコピーします。

1. ポータルで第 2 のサブスクリプションに移動します。 サブスクリプションを選び、 **[アクセス制御 (IAM)]** を選択します

1. **[追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。

1. **[ロール]** ドロップ ダウン リストから **[共同作成者]** ロールを選択します。

1. **[選択]** フィールドに Azure 関数アプリケーションの名前を入力します。 結果の中で関数名を選択します。

1. **[保存]** を選択して、変更をコミットします。

## <a name="configure-schedules-overview"></a>スケジュールの構成の概要

VM の起動と停止を制御するためのオートメーションの方法を管理するには、要件に基づいて、含まれているロジック アプリを 1 つ以上構成します。

- Scheduled - 起動と停止のアクションは、Azure Resource Manager とクラシックの VM に対して指定したスケジュールに基づきます。**ststv2_vms_Scheduled_start** と **ststv2_vms_Scheduled_stop** により、スケジュールされた起動と停止が構成されます。

- Sequenced - 起動と停止のアクションは、事前定義されたシーケンス処理タグの付いた VM を対象とするスケジュールに基づきます。 サポートされているのは、**sequencestart** および **sequencestop** の 2 つの名前付きタグのみです。 **ststv2_vms_Sequenced_start** と **ststv2_vms_Sequenced_stop** により、シーケンスされた起動と停止が構成されます。

    > [!NOTE]
    > このシナリオでサポートされるのは、Azure Resource Manager の VM のみです。

- AutoStop - この機能は、CPU 使用率に基づいて Azure Resource Manager とクラシックの両方の VM に対して停止アクションを実行するためにのみ使用されます。 また、スケジュールに基づく "*アクションの実行*" の場合もあります。この場合、VM に関するアラートが作成され、条件に基づいて停止アクションを実行するためのアラートがトリガーされます。**ststv2_vms_AutoStop** により、自動停止機能が構成されます。

追加のスケジュールが必要な場合は、Azure portal の **[クローン]** オプションを使用して、提供されているロジック アプリのいずれかを複製することができます。

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="ロジック アプリを複製するための [クローン] オプションの選択":::

## <a name="scheduled-start-and-stop-scenario"></a>スケジュールされた起動と停止のシナリオ

Azure Resource Manager とクラシック VM のスケジュールされた起動と停止のアクションを構成するには、次の手順を実行します。 たとえば、**ststv2_vms_Scheduled_start** のスケジュールを構成し、朝オフィスにいるときに起動し、**ststv2_vms_Scheduled_stop** のスケジュールに基づき、夕方退社するときにサブスクリプション全体のすべての VM を停止することができます。

VM を起動するだけのロジック アプリの構成はサポートされています。

シナリオごとに、アクションの対象を 1 つ以上のサブスクリプション、1 つまたは複数のリソース グループにすることができ、1 つ以上の VM を包含または除外リストに指定できます。 同じロジック アプリ内でまとめて指定することはできません。

1. [Azure portal](https://portal.azure.com) にサインインしてから、 **[ロジック アプリ]** に移動します。

1. スケジュールされた起動を構成する場合、ロジック アプリの一覧から **[ststv2_vms_Scheduled_start]** を選択します。 スケジュールされた停止を構成する場合は、 **[ststv2_vms_Scheduled_stop]** を選択します。

1. 左側のウィンドウで **[ロジック アプリ デザイナー]** を選択します。

1. ロジック アプリ デザイナーが表示されたら、デザイナー ウィンドウで **[繰り返し]** を選択して、ロジック アプリのスケジュールを構成します。 特定の繰り返しオプションの詳細については、[繰り返しタスクのスケジュール](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)に関する記事を参照してください。

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="ロジック アプリの繰り返しの頻度を構成する":::

1. デザイナー ウィンドウで、 **[関数] - [お試しください]** を選択して、ターゲット設定を構成します。 要求本文で、サブスクリプション内のすべてのリソース グループにわたって VM を管理する場合は、次の例に示すように要求本文を変更します。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    次の例のように、各値をコンマで区切って `subscriptions` 配列内に複数のサブスクリプションを指定します。

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    要求本文で、特定のリソース グループの VM を管理する場合は、次の例に示すように要求本文を変更します。 指定する各リソース パスは、コンマで区切る必要があります。 必要に応じて、リソース グループを 1 つ以上指定できます。

    この例では、仮想マシンの除外についても示しています。 VM を除外するには、VM のリソース パスを指定するかワイルドカードを使用します。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    ここでは、両方のサブスクリプションで、VM 名が Az と Bz で始まるもの以外のすべての VM に対してアクションが実行されます。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    要求本文で、サブスクリプション内の特定の VM セットを管理する場合は、次の例に示すように要求本文を変更します。 指定する各リソース パスは、コンマで区切る必要があります。 必要に応じて、VM を 1 つ指定できます。

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>シーケンス処理された起動と停止のシナリオ

分散アプリケーション アーキテクチャ内の複数の Azure Resource Manager VM に 2 つ以上のコンポーネントを含む環境では、コンポーネントの起動および停止順序を指定できることが重要です。

1. シーケンス処理された起動を構成する場合、ロジック アプリの一覧から **[ststv2_vms_Sequenced_start]** を選択します。 シーケンス処理された停止を構成する場合は、 **[ststv2_vms_Sequenced_stop]** を選択します。

1. 左側のウィンドウで **[ロジック アプリ デザイナー]** を選択します。

1. ロジック アプリ デザイナーが表示されたら、デザイナー ウィンドウで **[繰り返し]** を選択して、ロジック アプリのスケジュールを構成します。 特定の繰り返しオプションの詳細については、[繰り返しタスクのスケジュール](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)に関する記事を参照してください。

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="ロジック アプリの繰り返しの頻度を構成する":::

1. デザイナー ウィンドウで、 **[関数] - [お試しください]** を選択して、ターゲット設定を構成します。 要求本文で、サブスクリプション内のすべてのリソース グループにわたって VM を管理する場合は、次の例に示すように要求本文を変更します。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    次の例のように、各値をコンマで区切って `subscriptions` 配列内に複数のサブスクリプションを指定します。

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    要求本文で、特定のリソース グループの VM を管理する場合は、次の例に示すように要求本文を変更します。 指定する各リソース パスは、コンマで区切る必要があります。 必要に応じて、リソース グループを 1 つ指定できます。

    この例では、ワイルドカードを使用するスケジュールされた起動/停止の例と比較した、リソース パスによる仮想マシンの除外も示しています。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    要求本文で、サブスクリプション内の特定の VM セットを管理する場合は、次の例に示すように要求本文を変更します。 指定する各リソース パスは、コンマで区切る必要があります。 必要に応じて、VM を 1 つ指定できます。

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>自動停止シナリオ

Start/Stop VMs v2 (プレビュー) は、営業時間外などの非ピーク期間中に使用されていないマシンを評価し、プロセッサ使用率が指定されたパーセンテージ未満の場合は自動的にシャットダウンすることで、サブスクリプションで実行中の Azure Resource Manager およびクラシック VM のコストを管理する上で役立ちます。

要求本文の次のメトリック アラート プロパティでは、カスタマイズがサポートされています。

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Azure Monitor メトリック アラートのしくみと構成方法の詳細については、[Azure Monitor のメトリック アラート](../../azure-monitor/alerts/alerts-metric-overview.md)に関する記事を参照してください。

1. 自動停止を構成する場合、ロジック アプリの一覧から **[ststv2_vms_AutoStop]** を選択します。

1. 左側のウィンドウで **[ロジック アプリ デザイナー]** を選択します。

1. ロジック アプリ デザイナーが表示されたら、デザイナー ウィンドウで **[繰り返し]** を選択して、ロジック アプリのスケジュールを構成します。 特定の繰り返しオプションの詳細については、[繰り返しタスクのスケジュール](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)に関する記事を参照してください。

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="ロジック アプリの繰り返しの頻度を構成する":::

1. デザイナー ウィンドウで、 **[関数] - [お試しください]** を選択して、ターゲット設定を構成します。 要求本文で、サブスクリプション内のすべてのリソース グループにわたって VM を管理する場合は、次の例に示すように要求本文を変更します。

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    要求本文で、特定のリソース グループの VM を管理する場合は、次の例に示すように要求本文を変更します。 指定する各リソース パスは、コンマで区切る必要があります。 必要に応じて、リソース グループを 1 つ指定できます。

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    要求本文で、サブスクリプション内の特定の VM セットを管理する場合は、次の例に示すように要求本文を変更します。 指定する各リソース パスは、コンマで区切る必要があります。 必要に応じて、VM を 1 つ指定できます。

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>次のステップ

Start/Stop VMs v2 (プレビュー) 機能によって管理されている Azure VM の状態を監視し、その他の管理タスクを実行する方法については、[Start/Stop VMs の管理](manage.md)に関する記事を参照してください。
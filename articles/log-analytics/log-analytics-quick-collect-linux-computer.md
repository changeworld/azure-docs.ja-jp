---
title: "Azure Log Analytics を使用してオンプレミスの Linux コンピューターからデータを収集する | Microsoft Docs"
description: "Linux 用の Log Analytics エージェントをデプロイし、Log Analytics を使用した OS からのデータ収集を有効にする方法について説明します。"
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 66748adc49ef921ab8adb5306b2a483234b076ae
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>環境内でホストされている Linux コンピューターからデータを収集する
[Azure Log Analytics](log-analytics-overview.md) は、詳細な分析と相関のために、物理的または仮想的な Linux コンピューターおよびその他の環境内のリソースから直接データを 1 つのリポジトリに収集することができます。  このクイック スタートでは、いくつかの簡単な手順で、Linux コンピューターを構成し、データを収集する方法を示します。  Azure Linux VM の場合は、次のトピック「[Collect data about Azure Virtual Machines](log-analytics-quick-collect-azurevm.md)」 (Azure Virtual Machines に関するデータを収集する) を参照してください。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure-portal"></a>Azure Portal へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。 

## <a name="create-a-workspace"></a>ワークスペースの作成
1. Azure Portal で、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。<br><br> ![Azure ポータル](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. **[作成]** をクリックし、次の項目について選択します。

  * 新しい **OMS ワークスペース**の名前 (*DefaultLAWorkspace* など) を指定します。 
  * 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
  * **[リソース グループ]** では、1 つ以上の Azure Virtual Machines を含む既存のリソース グループを選択します。  
  * VM のデプロイ先となる**場所**を選択します。  詳細については、[Log Analytics を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照してください。
  * Log Analytics では 3 つの異なる**価格レベル**から選択できますが、このクイック スタートでは、**Free** レベルを選択します。  特定のレベルの詳細については、[Log Analytics の価格の詳細](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページを参照してください。

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. **[OMS ワークスペース]** ウィンドウに必要な情報を入力したら、**[OK]** をクリックします。  

情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。 

## <a name="obtain-workspace-id-and-key"></a>ワークスペース ID とキーを取得する
OMS エージェント for Linux をインストールする前に、Log Analytics ワークスペースのワークスペース ID とキーが必要です。  この情報は、エージェント ラッパー スクリプトがエージェントを適切に構成し、そのエージェントを Log Analytics と正常に通信できるようにするために必要です。  

1. Azure Portal で、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics ワークスペースの一覧で、前の手順で作成した *DefaultLAWorkspace* を選択します。
3. **[詳細設定]** を選択します。<br><br> ![Log Analytics の詳細設定](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. **[接続されたソース]**、**[Linux サーバー]** の順に選択します。   
5. **[ワークスペース ID]** と **[主キー]** の右側に値が表示されます。 両方をコピーしてお使いのエディターに貼り付けます。   

## <a name="install-the-agent-for-linux"></a>Linux 用エージェントのインストール
次の手順では、Azure および Azure Government クラウドで Log Analytics 用のエージェントのセットアップを構成します。  

>[!NOTE]
>Linux 用 OMS エージェントは、複数の Log Analytics ワークスペースにレポートするように構成することはできません。  

1. Log Analytics に接続できるように Linux コンピューターを構成するには、前にコピーしたワークスペース ID と主キーを指定した次のコマンドを実行します。  このコマンドは、エージェントをダウンロードし、そのチェックサムを検証し、エージェントをインストールします。 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government クラウドの Log Analytics に接続できるように Linux コンピューターを構成するには、前にコピーしたワークスペース ID と主キーを指定した次のコマンドを実行します。  このコマンドは、エージェントをダウンロードし、そのチェックサムを検証し、エージェントをインストールします。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>プロキシ サーバーと通信するエージェントを構成する

Linux コンピューターがプロキシ サーバー経由で Log Analytics と通信するようにする場合は、次の手順を実行します。  プロキシ構成の値には次の構文があります。`[protocol://][user:password@]proxyhost[:port]`

1. 次のコマンドを実行してファイル `/etc/opt/microsoft/omsagent/proxy.conf` を編集し、値を固有の設定に変更します。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. 次のコマンドを実行してエージェントを再起動します。 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>イベントとパフォーマンス データを収集する
Log Analytics は、イベントを Linux Syslog から収集でき、長期分析およびレポートのために指定されたパフォーマンス カウンターからも収集できます。また、特定の条件が検出された場合はアクションを実行できます。  まず、以下の手順に従って、Linux Syslog およびいくつかの一般的なパフォーマンス カウンターからのイベント収集を構成します。  

1. **[Syslog]** を選択します。  
2. イベント ログを追加するには、ログの名前を入力します。  「**Syslog**」と入力し、プラス記号 **+** をクリックします。  
3. テーブルで、重大度の **[情報]**、**[通知]** および **[デバッグ]** の各チェック ボックスをオフにします。 
4. ページの上部にある **[保存]** をクリックして構成を保存します。
5. **[Linux パフォーマンス データ]** を選択して、Windows コンピューターでのパフォーマンス カウンターの収集を有効にします。 
6. 新しい Log Analytics ワークスペースの Linux パフォーマンス カウンターを初めて構成する場合は、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。 それぞれのオプションの横には、チェック ボックスが表示されます。<br><br> ![既定の Windows パフォーマンス カウンターが選択されています](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)。<br><br> **[選択したパフォーマンス カウンターを追加する]** をクリックします。  カウンターが追加され、10 秒間の収集サンプル間隔でプリセットされます。  
7. ページの上部にある **[保存]** をクリックして構成を保存します。

## <a name="view-data-collected"></a>収集されたデータを表示する
これでデータ収集は有効になりました。次は、簡単なログ検索の例を実行して、ターゲット コンピューターのデータを表示してみましょう。  

1. Azure Portal で Log Analytics に移動し、前の手順で作成したワークスペースを選択します。
2. **[ログ検索]** タイルをクリックし、[ログ検索] ウィンドウのクエリ フィールドに「`Perf`」と入力してから Enter キーを押すか、クエリ フィールドの右側にある検索ボタンをクリックします。<br><br> ![Log Analytics のログ検索クエリの例](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> たとえば、次の画像のクエリでは、735 個のパフォーマンス レコードが返されています。<br><br> ![Log Analytics のログ検索の結果](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になった場合、Linux コンピューターからエージェントを削除し、Log Analytics ワークスペースを削除できます。  

エージェントを削除するには、次の手順を実行します。

1. Linux エージェントの[ユニバーサル スクリプト](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)をコンピューターにダウンロードします。
2. コンピューター上で引数 *--purge* を指定して .sh バンドル ファイルを実行します。これにより、エージェントとその構成が完全に削除されます。

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

ワークスペースを削除するには、作成した Log Analytics ワークスペースを選択し、リソース ページで **[削除]** をクリックします。<br><br> ![Log Analytics リソースを削除する](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>次のステップ
これでオンプレミスの Linux コンピューターからオペレーション データとパフォーマンス データが収集されているので、収集したデータの調査と分析、およびデータに対するアクションの実行を*無料*で簡単に開始することができます。  

データの表示および分析方法を学習する場合は、次のチュートリアルに進んでください。   

> [!div class="nextstepaction"]
> [Log Analytics でのデータの表示と分析](log-analytics-tutorial-viewdata.md)

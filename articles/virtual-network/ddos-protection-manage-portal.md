---
title: "Azure Portal を使用した Azure DDoS Protection Standard の管理 | Microsoft Docs"
description: "Azure Monitor で Azure DDoS Protection Standard テレメトリを使用して攻撃を軽減する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 019d4ba9124173a7de555c46d32881ecf639a34c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure Portal を使用した Azure DDoS Protection Standard の管理

分散型サービス拒否 (DDoS) Protection の有効と無効を切り替える方法と、Azure DDoS Protection Standard を使用して DDoS 攻撃を緩和するためにテレメトリを使用する方法について説明します。 DDoS Protection Standard は、Azure [パブリック IP アドレス](virtual-network-public-ip-address.md)が割り当てられた Azure リソース (仮想マシン、ロード バランサー、アプリケーション ゲートウェイなど) を保護します。 DDoS Protection Standard とその機能の詳細については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。 

>[!IMPORTANT]
>Azure DDoS Protection Standard (DDoS Protection) は、現在プレビュー段階にあります。 DDoS Protection がサポートされる Azure リソース数と使用可能なリージョンは限られています。 使用可能なリージョンの一覧については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。 サブスクリプション用に DDoS Protection を有効にするには、制限されたプレビュー中に[このサービスに登録する](http://aka.ms/ddosprotection)必要があります。 登録すると、Azure DDoS チームから連絡があり、有効化プロセスが案内されます。 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>DDoS Protection Standard を有効にする - 新しい仮想ネットワーク

1. Azure Portal (http://portal.azure.com) にログインします。Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
2. Azure Portal の左上にある **[新規]** ボタンをクリックします。
3. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
4. 任意に選択した設定で、仮想ネットワークを作成します。 仮想ネットワークの作成の詳細については、「[複数のサブネットを含んだ仮想ネットワークを作成する](virtual-networks-create-vnet-arm-pportal.md)」を参照してください。 *[DDoS 保護]* で、**[有効]** をクリックしてから、**[作成]** をクリックします。

    ![Create virtual network](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > リージョンを選択する場合は、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」に記載された、サポートされているリージョンの一覧から選択してください。

    DDoS Protection を有効にすると課金が発生するという警告が表示されます。 プレビュー中、DDoS Protection に対する課金は発生しません。 一般公開の時点から課金が発生します。 お客様は、課金と一般公開の開始 30 日前に通知を受け取ります。

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>DDoS Protection Standard を有効にする - 既存仮想ネットワーク 

1. Azure Portal メニューの **[仮想ネットワーク]** をクリックしてから、仮想ネットワークを選択します。
2. **}[DDoS Protection]** をクリックし、*}[DDoS Protection]* 画面で **[有効]** をクリックしてから、**[保存]** をクリックします。 

    > [!WARNING]
    > 仮想ネットワークは、サポートされているリージョン内に存在している必要があります。 サポートされているリージョンの一覧については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。

    DDoS Protection を有効にすると課金が発生するという警告が表示されます。 プレビュー中、DDoS Protection に対する課金は発生しません。 一般公開の時点から課金が発生します。 お客様は、課金と一般公開の開始 30 日前に通知を受け取ります。

## <a name="disable-ddos-protection-on-a-virtual-network"></a>仮想ネットワーク上の DDoS Protection を無効にする

1. Azure Portal メニューの **[仮想ネットワーク]** をクリックしてから、仮想ネットワークを選択します。
2. **}[DDoS Protection]** をクリックし、*[DDoS Protection]* 画面で **[無効]** をクリックしてから、**[保存]** をクリックします。

## <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS Protection メトリックにアラートを構成する

Azure Monitor のアラート構成を使用すると、利用可能な DDoS Protection メトリックをどれでも選び、攻撃中に有効な緩和策がある場合にそのメトリックのアラートを受けることができます。 条件が満たされると、指定したアドレスにアラート メールが届きます。

1. **[監視]** をクリックしてから、**[メトリック]** をクリックします。
2. *[メトリック]* 画面で、リソース グループ、**[パブリック IP アドレス]** のリソースの種類、Azure のパブリック IP アドレスを選択します。
3. メトリックの電子メール アラートを構成するには、**[メトリック アラートの追加]** をクリックします。 電子メール アラートは任意のメトリックに作成できますが、最も明らかなメトリックは **[Under DDoS attack or not] \(DDoS 攻撃を受けているかどうか)** です。 これはブール値 1 または 0 です。 **1** は、攻撃を受けていることを示します。 **0** は、攻撃を受けていないことを示します。
4. 攻撃を受けている場合に電子メールを受信するようにするには、**[Under DDoS attack or not] (DDoS 攻撃を受けているかどうか)** と **[Condition to Greater than zero (0) over the last 5 minutes] (過去 5 分間にゼロ (0) を超えた条件)** のメトリックを設定します。 他のメトリックに対して同様のアラートを設定できます。

    ![メトリックを構成する](./media/ddos-protection-manage-portal/ddos-metrics.png)

    攻撃の検出から数分以内に、Azure Monitor メトリックを使用してユーザーに通知します。

    ![攻撃アラート](./media/ddos-protection-manage-portal/ddos-alert.png) 

また、[webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-what-are-logic-apps.md)の詳細についても学習できます。

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>DDoS Protection Standard メトリックへのログ記録を構成する

1. **[監視]** をクリックしてから、**[診断設定]** をクリックします。
2. *[メトリック]* 画面で、リソース グループ、**[パブリック IP アドレス]** のリソースの種類、Azure のパブリック IP アドレスを選択します。
3. **[Turn on diagnostics to collect the following data] \(診断を有効にして次のデータを収集する)** をクリックします。

ログ記録に使用できるオプションとして、次の 3 つがあります。

- **[ストレージ アカウントへのアーカイブ]**: ログをストレージ アカウントに書き込みます。
- **[イベント ハブへのストリーミング]**: ログの受信者がイベント ハブを使用してログを取得できるようにします。 これにより、Splunk やその他の SIEM システムとの統合が可能になります。
- **[Log Analytics への送信]**: ログを Azure OMS Log Analytics サービスに書き込みます。

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection テレメトリを使用する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 このテレメトリは、パブリック IP アドレスが軽減策のもとにある期間中のみ使用できます。 攻撃が軽減される前または軽減された後、テレメトリは表示されません。

1. **[監視]** をクリックしてから、**[メトリック]** をクリックします。 
2. *[メトリック]* 画面で、リソース グループ、**[パブリック IP アドレス]** のリソースの種類、Azure のパブリック IP アドレスを選択します。 一連の **[利用可能なメトリック]** が画面の左側に表示されます。 これらのメトリックは、選択すると、概要画面の **Azure Monitor のメトリック グラフ**に表示されます。 

メトリック名は、異なるパケットの種類と、バイト数かパケット数かを表します。各メトリックでのタグ名の基本的な構成は、次のようになっています。

- **削除された "タグ名" (たとえば、削除された受信パケット DDoS):** DDoS Protection システムによって削除/除去されたパケットの数。
- **転送された "タグ名" (たとえば、転送された受信パケット DDoS):** DDoS システムによって宛先 VIP に転送されたパケットの数 (フィルター処理されなかったトラフィック)。
- **タグ名なし (たとえば、受信パケット DDoS):** 除去システムに入力されたパケットの総数 (削除および転送されたパケットの総数を表します)。

## <a name="next-steps"></a>次のステップ

- [Azure 診断ログの詳細を確認する](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
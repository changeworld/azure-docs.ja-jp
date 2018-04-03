---
title: Load Balancer のカスタム プローブを使用して正常性の状態を監視する | Microsoft Docs
description: Azure Load Balancer でカスタム プローブを使用して、Load Balancer の背後にあるインスタンスを監視する方法を説明します
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="understand-load-balancer-probes"></a>Load Balancer のプローブを理解する

Azure Load Balancer では、正常性プローブを使用して、どのバックエンド プール インスタンスが新しいフローを受信する必要があるかを決定します。 正常性プローブが失敗すると、Azure Load Balancer は、問題のある各インスタンスへの新しいフローの送信を停止しますが、そのインスタンス上の既存のフローは影響を受けません。  すべてのバックエンド プール インスタンス プローブがダウン している場合は、すべての既存のフローはバックエンド プール内のすべてのインスタンスでタイムアウトします。

クラウド サービス ロール (worker ロールと Web ロール) では、ゲスト エージェントを使用してプローブを監視します。 Azure Load Balancer の背後にある VM を使用する場合は、TCP または HTTP カスタム正常性プローブを構成する必要があります。

## <a name="understand-probe-count-and-timeout"></a>プローブの数とタイムアウトについて

プローブの動作は、以下の要素によって変わってきます。

* インスタンスを実行中としてラベル付けできる成功プローブの数。
* インスタンスが停止中としてラベル付けされる失敗プローブの数。

インスタンスが実行中か停止中かは、SuccessFailCount に設定されているタイムアウトと頻度の値によって決まります。 Azure ポータルの場合、タイムアウトは頻度の値の 2 倍に設定されます。

エンドポイントに対して負荷分散されたすべてのインスタンス (負荷分散されたセット) のプローブ構成は、同じにする必要があります。 特定のエンドポイントの組み合わせに対してホストされる同じサービス内の各ロール インスタンスまたは VM に、異なるプローブ構成を設定することはできません。 たとえば、各インスタンスには、同一のローカル ポートとタイムアウトが必要です。

> [!IMPORTANT]
> Load Balancer のプローブは、IP アドレス 168.63.129.16 を使います。 このパブリック IP アドレスを使うことで、独自の IP Azure Virtual Network を使用するシナリオで、内部プラットフォーム リソースへの通信が容易になります。 仮想パブリック IP アドレス 168.63.129.16 は、すべてのリージョンで使用され、変更されることはありません。 この IP アドレスは、すべてのローカル ファイアウォール ポリシーで許可することをお勧めします。 これをセキュリティ リスクとして考慮する必要はありません。内部 Azure プラットフォームのみが、そのアドレスからのメッセージをソースにできるためです。 ファイアウォール ポリシーでこの IP アドレスを許可しないと、さまざまなシナリオで予期しない動作が発生します。 たとえば、168.63.129.16 の同じ IP アドレス範囲が構成されて、IP アドレスが重複することがあります。

## <a name="learn-about-the-types-of-probes"></a>プローブの種類について

### <a name="guest-agent-probe"></a>ゲスト エージェント プローブ

ゲスト エージェント プローブは、Azure Cloud Services でのみ使用できます。 Load Balancer は、VM 内のゲスト エージェントを利用します。 その後リッスンし、インスタンスが準備完了状態になっている場合にのみ、HTTP 200 OK で応答します  (他の状態はビジー、リサイクル中、停止中です)。

詳しくは、[正常性プローブのサービス定義ファイル (csdef) の構成](https://msdn.microsoft.com/library/azure/ee758710.aspx)に関するページまたは[クラウド サービス用のパブリック ロード バランサーの作成の開始](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)に関するページをご覧ください。

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>ゲスト エージェント プローブがインスタンスを異常としてマークする状況

ゲスト エージェントが HTTP 200 OK で応答できない場合、Load Balancer はそのインスタンスを応答不能としてマークします。 そして、インスタンスへのトラフィックの送信を停止します。 Load Balancer はインスタンスへの ping を続けます。 ゲスト エージェントが HTTP 200 で応答すると、Load Balancer はそのインスタンスへのトラフィックの送信を再開します。

Web ロールを使う場合、Web サイト コードは通常、Azure ファブリックやゲスト エージェントでは監視されない w3wp.exe で実行されます。 w3wp.exe でのエラー (たとえば、HTTP 500 の応答) は、ゲスト エージェントに報告されません。 したがって、Load Balancer はそのインスタンスをローテーションから外しません。

### <a name="http-custom-probe"></a>HTTP カスタム プローブ

HTTP カスタム プローブは、既定のゲスト エージェント プローブを上書きします。 ユーザーは独自のカスタム ロジックを作成し、ロール インスタンスの正常性を判断できます。 Load Balancer は、既定では 15 秒ごとにエンドポイントを調査します。 タイムアウト期間内にインスタンスが HTTP 200 で応答した場合、そのインスタンスは Load Balancer ローテーション内にあると見なされます。 タイムアウト期間の既定値は 31 秒です。

HTTP カスタム プローブは、Load Balancer ローテーションからインスタンスを削除するユーザー独自のロジックを実装する必要がある場合に役立ちます。 たとえば、CPU が 90% を超え、200 以外の状態が返される場合は、そのインスタンスが削除されるようにすることができます。 w3wp.exe を使う Web ロールがある場合は、Web サイトの自動監視も取得します。 Web サイトのコードで障害が発生すると、200 以外の状態がLoad Balancer プローブに返ります。

> [!NOTE]
> HTTP カスタム プローブは、相対パスと HTTP プロトコルのみをサポートします。 HTTPS はサポートされません。

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>HTTP カスタム プローブがインスタンスを異常としてマークする状況

* HTTP アプリケーションが 200 以外の HTTP 応答コード (403、404、500 など) を返す。 この肯定応答は、アプリケーション インスタンスをすぐにサービスから外すように警告するものです。
* タイムアウト期間後、HTTP サーバーがまったく応答しない。 設定されているタイムアウト値によっては、プローブが停止中としてマークされる前に (つまり、SuccessFailCount プローブが送信される前に)、複数のプローブ要求が応答なしになる可能性があります。
* サーバーが TCP リセットによって接続を閉じた。

### <a name="tcp-custom-probe"></a>TCP カスタム プローブ

TCP カスタム プローブは、定義済みのポートに 3 ウェイ ハンドシェイクを実行して、接続を開始します。

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>TCP カスタム プローブがインスタンスを異常としてマークする状況

* タイムアウト期間後、TCP サーバーがまったく応答しない。 プローブが停止中としてマークされるタイミングは、プローブが停止中としてマークされる前に応答なしになるように構成された、失敗したプローブ要求の数によって異なります。
* プローブがロール インスタンスから TCP リセットを受信した。

HTTP 正常性プローブまたは TCP プローブを構成する方法について詳しくは、[リソース マネージャーで PowerShell を使ってパブリック ロード バランサーの作成を開始する方法](load-balancer-get-started-internet-arm-ps.md)に関するページをご覧ください。

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>正常なインスタンスを Load Balancer ローテーションに戻す

次の場合に、TCP と HTTP プローブは正常と見なされ、ロール インスタンスを正常としてマークします。

* VM の初回起動時に、Load Balancer が正のプローブを取得します。
* (前述した) SuccessFailCount の数により、ロール インスタンスを正常としてマークするために必要な成功プローブの値が定義されます。 ロール インスタンスが削除された場合、そのロール インスタンスを実行中としてマークするには、連続して成功したプローブの数は、SuccessFailCount の値以上である必要があります。

> [!NOTE]
> ロール インスタンスの正常性が変動する場合、Load Balancer は、さらに長い時間待機してから、ロール インスタンスを正常な状態に戻します。 この余分な待機時間は、ユーザーとインフラストラクチャを保護するためであり、意図的なポリシーです。

## <a name="use-log-analytics-for-a-load-balancer"></a>Load Balancer のログ分析を使用する

[ログ分析](load-balancer-monitor-log.md)を使って、Load Balancer プローブの正常性状態とプローブの数を確認できます。 ログ記録と共に Power BI または Azure Operational Insights を使用することで、Load Balancer の正常性状態の統計情報を提供することができます。

---
title: Load Balancer の正常性プローブを使用してサービスを保護する | Microsoft Docs
description: 正常性プローブを使用して Load Balancer の背後にあるインスタンスを監視する方法について説明します
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2018
ms.author: kumud
ms.openlocfilehash: 58bc0c0669992b8b3884e24c39862f47412b9110
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584657"
---
# <a name="load-balancer-health-probes"></a>Load Balancer の正常性プローブ

Azure Load Balancer は、正常性プローブを使用して、どのバックエンド プール インスタンスが新しいフローを受信するかを特定します。 バックエンド インスタンス上のアプリケーションの障害を検出するのに正常性プローブを使用することができます。 また、正常性プローブへのカスタム応答を生成したり、フロー制御や、バックエンド インスタンスへの新しいフローの送信を続けるか停止するかの Load Balancer への指示に、正常性プローブを使用したりすることもできます。 これを使用して、負荷または計画的ダウンタイムを管理できます。 正常性プローブが失敗した場合、Load Balancer は、それぞれの異常なインスタンスへの新しいフローの送信を停止します。

使用できる正常性プローブの種類と正常性プローブの動作方法は、使用している Load Balancer の SKU によって異なります。 たとえば、新規フローおよび既存フローの動作は、フローが TCP か UDP かということ、および使用されている Load Balancer SKU に依存します。

| | Standard SKU | Basic SKU |
| --- | --- | --- |
| [プローブの種類](#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [プローブのダウン動作](#probedown) | すべてのプローブがダウンすると、すべての TCP フローは続行します。 | すべてのプローブがダウンすると、すべての TCP フローは終了します。 | 

> [!IMPORTANT]
> Load Balancer の正常性プローブは IP アドレス 168.63.129.16 から送信され、プローブでインスタンスをアップとしてマークするにはブロックされてはなりません。  詳しくは、「[プローブのソース IP アドレス](#probesource)」をご覧ください。

## <a name="types"></a>プローブの種類

正常性プローブは、実際のサービスが提供されているポートも含めて、バックエンド インスタンスの任意のポートを監視できます。 正常性プローブ プロトコルは、次の 3 種類の正常性プローブに合わせて構成できます。

- [TCP リスナー](#tcpprobe)
- [HTTP エンドポイント](#httpprobe)
- [HTTPS エンドポイント](#httpsprobe)

使用できる正常性プローブの種類は、選択した Load Balancer SKU によって変わります。

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard SKU |    &#9989; |   &#9989; |   &#9989; |
| Basic SKU |   &#9989; |   &#9989; | &#10060; |

UDP の負荷分散では、TCP、HTTP、または HTTPS の正常性プローブを使用して、バックエンド インスタンスに対するカスタム正常性プローブ シグナルを生成する必要があります。

[Standard Load Balancer](load-balancer-standard-overview.md) で [HA ポート負荷分散ルール](load-balancer-ha-ports-overview.md)を使用すると、すべてのポートが負荷分散されて、1 つの正常性プローブ応答がインスタンス全体の状態を反映する必要があります。  

VNet 内の別のインスタンスに対する正常性プローブを受信するインスタンスを通過する正常性プローブに対し、NAT またはプロキシを行ってはなりません。行うと、シナリオで障害が連鎖する可能性があります。

正常性プローブをテストする場合、または個別のインスタンスをダウンとしてマークする場合は、セキュリティ グループを使用して正常性プローブを明示的にブロックできます (ターゲットまたは[ソース](#probesource))。

### <a name="tcpprobe"></a> TCP プローブ

TCP プローブは、定義済みのポートに 3 ウェイ オープン TCP ハンドシェイクを実行して、接続を開始します。  その後、4 ウェイ クローズ TCP ハンドシェイクが行われます。

最小のプローブ間隔は 5 秒で、異常な応答の最小数は 2 です。  合計継続時間が 120 秒を超えることはできません。

次の場合、TCP プローブは失敗します。
* タイムアウト期間中に、インスタンスで TCP リスナーがまったく応答しない場合。  プローブは、失敗したプローブ要求の数に基づいて、ダウンとしてマークされます。プローブ要求は、プローブがダウンとしてマークされる前に、応答なしになるように構成されています。
* プローブがインスタンスから TCP リセットを受信した場合。

#### <a name="resource-manager-template"></a>Resource Manager テンプレート

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS プローブ

> [!NOTE]
> HTTPS プローブは、[Standard Load Balancer](load-balancer-standard-overview.md) でのみ使用できます。

HTTP および HTTPS プローブは TCP 接続を確立し、指定されたパスで HTTP GET を発行します。 これら両方のプローブは、HTTP GET に対して相対パスをサポートします。 HTTPS プローブは、トランスポート層セキュリティ (TLS、旧称は SSL) ラッパーがある点を除き、HTTP プローブと同じです。 タイムアウト期間内にインスタンスが HTTP ステータス 200 で応答すると、正常性プローブはアップとしてマークされます。  既定では、これらの正常性プローブは構成されている正常性プローブ ポートを 15 秒ごとに確認します。 最小のプローブ間隔は 5 秒です。 合計継続時間が 120 秒を超えることはできません。 

また、HTTP/HTTPS プローブは、Load Balancer ローテーションからインスタンスを削除するユーザー独自のロジックを実装する必要がある場合にも役立ちます。 たとえば、CPU が 90% を超え、200 以外の HTTP ステータスが返される場合は、そのインスタンスが削除されるようにすることができます。 

Cloud Services を使用し、w3wp.exe を使う Web ロールがある場合は、Web サイトの自動監視も実現します。 Web サイトのコードで障害が発生すると、200 以外の状態がLoad Balancer プローブに返ります。  HTTP プローブは、既定のゲスト エージェント プローブをオーバーライドします。 

次の場合、HTTP/HTTPS プローブは失敗します。
* プローブ エンドポイントが、200 以外の HTTP 応答コード (403、404、500 など) を返す場合。 これにより、正常性プローブはすぐにダウンとしてマークされます。 
* 31 秒のタイムアウト期間内に、プローブ エンドポイントがまったく応答しない場合。 設定されているタイムアウト値によっては、プローブが停止中としてマークされる前に (つまり、SuccessFailCount プローブが送信される前に)、複数のプローブ要求が応答なしになる可能性があります。
* プローブ エンドポイントが TCP リセットによって接続を閉じた場合。

#### <a name="resource-manager-templates"></a>Resource Manager テンプレート

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>ゲスト エージェント プローブ (クラシックのみ)

既定では、クラウド サービス ロール (worker ロールと Web ロール) は、ゲスト エージェントを使用してプローブを監視します。   このオプションは最後の手段として考える必要があります。  常に、TCP プローブまたは HTTP プローブで明示的に正常性プローブを定義する必要があります。 ほとんどのアプリケーション シナリオでは、ゲスト エージェント プローブは明示的に定義されたプローブほど効果的ではありません。  

ゲスト エージェント プローブは、VM 内のゲスト エージェントのチェックです。 その後リッスンし、インスタンスが準備完了状態になっている場合にのみ、HTTP 200 OK で応答します  (他の状態はビジー、リサイクル中、停止中です)。

詳しくは、[正常性プローブのサービス定義ファイル (csdef) の構成](https://msdn.microsoft.com/library/azure/ee758710.aspx)に関するページまたは[クラウド サービス用のパブリック ロード バランサーの作成の開始](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)に関するページをご覧ください。

ゲスト エージェントが HTTP 200 OK で応答できない場合、Load Balancer はそのインスタンスを応答不能としてマークします。 そして、そのインスタンスへのフローの送信を停止します。 Load Balancer はインスタンスのチェックを続けます。 

ゲスト エージェントが HTTP 200 で応答すると、Load Balancer はそのインスタンスへの新しいフローの送信を再開します。

Web ロールを使う場合、Web サイト コードは通常、Azure ファブリックやゲスト エージェントでは監視されない w3wp.exe で実行されます。 w3wp.exe でのエラー (たとえば、HTTP 500 の応答) は、ゲスト エージェントに報告されません。 したがって、Load Balancer はそのインスタンスをローテーションから外しません。

## <a name="probehealth"></a>プローブの正常性

次の場合、TCP、HTTP、および HTTPS の正常性プローブは正常と見なされ、ロール インスタンスを正常としてマークします。

* VM が初めて起動するときに正常性プローブが成功します。
* (前述した) SuccessFailCount の数により、ロール インスタンスを正常としてマークするために必要な成功プローブの値が定義されます。 ロール インスタンスが削除された場合、そのロール インスタンスを実行中としてマークするには、連続して成功したプローブの数は、SuccessFailCount の値以上である必要があります。

> [!NOTE]
> ロール インスタンスの正常性が変動する場合、Load Balancer は、さらに長い時間待機してから、ロール インスタンスを正常な状態に戻します。 この余分な待機時間は、ユーザーとインフラストラクチャを保護するためであり、意図的なポリシーです。

## <a name="probe-count-and-timeout"></a>プローブの数とタイムアウト

プローブの動作は、以下の要素によって変わってきます。

* インスタンスをアップとしてマークできる成功プローブの数。
* インスタンスがダウンとしてマークされる失敗プローブの数。

インスタンスが実行中か停止中かは、SuccessFailCount に設定されているタイムアウトと頻度の値によって決まります。 Azure ポータルの場合、タイムアウトは頻度の値の 2 倍に設定されます。

負荷分散規則では、それぞれのバックエンド プールで 1 つの正常性プローブが定義されています。

## <a name="probedown"></a>プローブのダウン動作

### <a name="tcp-connections"></a>TCP 接続

バックエンド インスタンスが正常であり、新しいフローを受け付けることができるゲスト OS とアプリケーションがある場合、そのインスタンスへの新しい TCP 接続は成功します。

バックエンド インスタンスの正常性プローブが失敗した場合、そのバックエンド インスタンスに対する確立済みの TCP 接続は続行されます。

バックエンド プール内のすべてのインスタンスのすべてのプローブが失敗した場合、そのバックエンド プールに新しいフローは送信されません。 Standard Load Balancer は、確立済みの TCP フローが続行するのを許可します。  Basic Load Balancer は、バックエンド プールに対するすべての既存の TCP フローを終了します。
 
フローは常にクライアントと VM のゲスト OS の間で行われるため、プールのすべてのプローブがダウンすると、フローを受信する正常なバックエンド インスタンスが存在しないため、フロントエンドは TCP 接続オープンの試行に応答しなくなります。

### <a name="udp-datagrams"></a>UDP データグラム

UDP データグラムは、正常なバックエンド インスタンスに配信されます。

UDP はコネクションレスであり、UDP に追跡されるフロー状態は存在しません。 いずれかのバックエンド インスタンスの正常性プローブが失敗すると、既存の UDP フローはバックエンド プール内の別の正常なインスタンスに移動できます。

バックエンド プール内のすべてのインスタンスのすべてのプローブが失敗した場合は、Basic および Standard の Load Balancer のすべての既存 UDP フローが終了します。

## <a name="probesource"></a>プローブのソース IP アドレス

Load Balancer は、その内部正常性モデルに対して分散プローブ サービスを使用します。 VM が存在する各ホストは、お客様の構成ごとに正常性プローブを生成するようにプログラミングできます。 正常性プローブのトラフィックは、正常性プローブを生成するインフラストラクチャ コンポーネントと、お客様の VM の間で直接やり取りされます。 Load Balancer のすべての正常性プローブは、ソースとして IP アドレス 168.63.129.16 から送信されます。  Azure の Virtual Network に独自の IP アドレスを追加するとき、この正常性プローブのソース IP アドレスは、Microsoft でグローバルに予約されているので、一意性が保証されます。  このアドレスはすべてのリージョンで同じであり、変更されません。 この IP アドレスからパケットを送信できるのは 内部 Azure プラットフォームだけなので、これをセキュリティ リスクとして考慮する必要はありません。 

Load Balancer の正常性プローブだけでなく、次の操作でもこの IP アドレスが使用されます。

- VM エージェントを、プラットフォームと通信して "準備完了" 状態を通知できるようにします
- カスタム DNS サーバーを定義していないお客様にフィルター処理された名前解決を提供するため、DNS 仮想サーバーとの通信を有効にします。  このフィルター処理により、お客様はデプロイのホスト名だけを確実に解決できます。

Load Balancer の正常性プローブがインスタンスをアップとしてマークするためには、Azure の[セキュリティ グループ](../virtual-network/security-overview.md)とローカル ファイアウォールのポリシーで、この IP アドレスを許可する**必要があります**。

ファイアウォール ポリシーでこの IP アドレスを許可しない場合、正常性プローブはインスタンスに到達できないため失敗します。  さらに、正常性プローブが失敗するため、Load Balancer はインスタンスをダウンとしてマークします。  これにより、負荷分散されているサービスが失敗する可能性があります。 

また、168.63.129.16 を含む Microsoft 所有の IP アドレス範囲で、VNet を構成してはなりません。  構成すると、正常性プローブの IP アドレスと競合します。

VM に複数のインターフェイスがある場合は、プローブを受信したインターフェイスでプローブに応答することを保証する必要があります。  そのためには、インターフェイスごとに VM でこのアドレスの一意のソース NAT を行うことが必要な場合があります。

## <a name="monitoring"></a>監視

公開および内部の [Standard Load Balancer](load-balancer-standard-overview.md) はいずれも、Azure Monitor を使用してエンドポイントおよびバックエンド インスタンスの正常性プローブ状態を多次元メトリックとして公開します。 これは、他の Azure サービスやサード パーティ製のアプリケーションによって消費される可能性があります。 

公開の Basic Load Balancer では、Log Analytics 経由でバックエンド プールごとにまとめられた正常性プローブの状態が公開されます。  これは、内部の Basic Load Balancer では使用できません。  [ログ分析](load-balancer-monitor-log.md)を使って、パブリック Load Balancer のプローブの正常性状態とプローブの数を確認できます。 ログ記録と共に Power BI または Azure Operational Insights を使用することで、Load Balancer の正常性状態の統計情報を提供することができます。

## <a name="limitations"></a>制限事項

-  HTTPS プローブは、クライアント証明書との相互認証をサポートしていません。

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する
- [PowerShell を使用した Resource Manager でのパブリック ロード バランサーの作成の概要](load-balancer-get-started-internet-arm-ps.md)
- [正常性プローブ用の REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- [Load Balancer の Uservoice](https://aka.ms/lbuservoice) で新しい正常性プローブ機能をリクエストする

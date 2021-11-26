---
title: デバイスまたはアプライアンスの CEF 形式のログを Microsoft Sentinel に取得する | Microsoft Docs
description: Linux ベースのログ フォワーダーにインストールされている Log Analytics エージェントを使用して、Syslog を介して Common Event Format (CEF) で送信されたログを Microsoft Sentinel ワークスペースに取り込みます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f13ef1ee30cb6e40db37228ca89eaacbda56814f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716184"
---
# <a name="get-cef-formatted-logs-from-your-device-or-appliance-into-microsoft-sentinel"></a>デバイスまたはアプライアンスの CEF 形式のログを Microsoft Sentinel に取得する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

ネットワークとセキュリティに関する多くのデバイスおよびアプライアンスは、システム ログを Common Event Format (CEF) として知られる特殊な形式で Syslog を介して送信します。 この形式には標準の Syslog 形式よりも多くの情報が含まれ、解析されたキーと値の配置で情報が示されます。 Log Analytics エージェントでは CEF ログを受け入れ、特に Microsoft Sentinel で使用するためにそれらのログの形式を設定してから、Microsoft Sentinel ワークスペースに転送します。

この記事では、CEF 形式のログを使用してデータ ソースを接続するプロセスについて説明します。 この方法を使用するデータ コネクタの詳細については、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md)に関するページを参照してください。

この接続の作成には、次の 2 つの手順が必要になります。これらについては後ほど詳しく説明します。

- Linux マシンまたは VM を専用のログ フォワーダーとして指定し、それに Log Analytics エージェントをインストールし、ログを Microsoft Sentinel ワークスペースに転送するようにエージェントを構成する。 エージェントのインストールと構成は、配置スクリプトによって処理されます。

- Syslog サーバーに対して CEF 形式でログを送信するようにデバイスを構成する。

> [!NOTE]
> データは、Microsoft Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="supported-architectures"></a>サポートされているアーキテクチャ

次の図は、Azure で Linux VM を使用した場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

または、別のクラウドの VM かオンプレミスのマシンを使用する場合は、次の構成を使用します。

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="prerequisites"></a>前提条件

Log Analytics に CEF データを取り込むためには、Microsoft Sentinel ワークスペースが必要です。

- このワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- このワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/agent-windows.md)を参照してください。

## <a name="designate-a-log-forwarder-and-install-the-log-analytics-agent"></a>ログ フォワーダーを指定し、Log Analytics エージェントをインストールする

このセクションでは、使用しているデバイスから Microsoft Sentinel ワークスペースにログを転送する Linux マシンを指定および構成する方法について説明します。

Linux マシンは、オンプレミス環境の物理マシンや仮想マシン、Azure VM のほか、別のクラウドの VM でもかまいません。

**Common Event Format (CEF) データ コネクタ ページ** のリンクを使用して、指定したコンピューターでスクリプトを実行し、次のタスクを実行します。

- **Linux 用 Log Analytics エージェント** ("OMS エージェント" ともいいます) をインストールし、次の用途に構成します。
    - 組み込みの Linux Syslog デーモンからの CEF メッセージを TCP ポート 25226 でリッスンする
    - メッセージの解析とエンリッチが行われる Microsoft Sentinel ワークスペースに対し、TLS で安全にメッセージを送信する

- **組み込みの Linux Syslog デーモン** (rsyslog.d/syslog-ng) を次の用途に構成します。
    - セキュリティ ソリューションからの Syslog メッセージを TCP ポート 514 でリッスンする
    - CEF として識別したメッセージだけを localhost の Log Analytics エージェントに TCP ポート 25226 を使用して転送する

詳細については、「[ログ フォワーダーをデプロイして Syslog および CEF ログを Microsoft Sentinel に取り込む](connect-log-forwarder.md)」を参照してください。

### <a name="security-considerations"></a>セキュリティに関する考慮事項

組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。

詳細については、[Azure での VM の保護](../virtual-machines/security-policy.md)、および[ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)に関する記事を参照してください。

ログ フォワーダーがクラウド内にある場合など、デバイスが TLS 経由で Syslog および CEF ログを送信している場合は、TLS で通信するように Syslog デーモン (rsyslog または syslog-ng) を構成する必要があります。 

詳細については、次を参照してください。

- [TLS を使用した Syslog トラフィックの暗号化 – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [TLS を使用したログ メッセージの暗号化 – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="configure-your-device"></a>デバイスを構成する

ログを CEF 形式で SIEM またはログ サーバーに送信するための、デバイス ベンダーが提供している構成手順を見つけてそれに従います。 

データ コネクタ ギャラリーに製品が表示されている場合は、詳細について [Microsoft Sentinel データ コネクタ リファレンス](data-connectors-reference.md)に関するページを参照できます。以下のリストの設定を含む構成手順が記載されています。

   - プロトコル = TCP
   - ポート = 514
   - 形式 = CEF
   - IP アドレス - CEF メッセージを、この目的専用の仮想マシンの IP アドレスに送信していることを確認します。

このソリューションは、Syslog RFC 3164 または RFC 5424 をサポートしています。

> [!TIP]
> ログ フォワーダーの Syslog デーモンでも同じ変更を行う場合は、必要に応じて、デバイスに別のプロトコルまたはポート番号を定義します。
>

## <a name="find-your-data"></a>データの検索

接続が確立されてからデータが Log Analytics に表示されるまでに、最大で 20 分かかる場合があります。

Log Analytics で CEF イベントを検索するには、クエリ ウィンドウで `CommonSecurityLog` テーブルに対してクエリを実行します。

データ コネクタ ギャラリーに表示される一部の製品では、最適な結果を得るには追加のパーサーを使用する必要があります。 これらのパーサーは、Kusto 関数を使用することによって実装されます。 詳細については、[Microsoft Sentinel データ コネクタ リファレンス](data-connectors-reference.md)に関するページで、お使いの製品に関するセクションを参照してください。

これらの製品の CEF イベントを検索するには、"CommonSecurityLog" ではなく、Kusto 関数の名前をクエリ サブジェクトとして入力します。

Microsoft Sentinel ポータルにある、お使いの製品のデータ コネクタ ページの **[次のステップ]** タブで、特にその製品用に作成された便利なサンプル クエリ、ブック、分析ルール テンプレートを見つけることができます。

データが何も表示されない場合は、[CEF のトラブルシューティング](./troubleshooting-cef-syslog.md)に関するページを参照してください。

### <a name="changing-the-source-of-the-timegenerated-field"></a>TimeGenerated フィールドのソースの変更

既定では、Log Analytics エージェントでは、エージェントが Syslog デーモンからイベントを受信した時刻がスキーマの *TimeGenerated* フィールドに設定されます。 そのため、ソース システムでイベントが生成された時刻は Microsoft Sentinel に記録されません。

ただし、次のコマンドを実行すると、 `TimeGenerated.py` スクリプトをダウンロードして実行できます。 このスクリプトにより、エージェントによる受信時刻ではなくソース システムの元の時刻を *TimeGenerated* フィールドに設定するように、Log Analytics エージェントが構成されます。

```bash
wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
```

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft Sentinel によってデバイスやアプライアンスから CEF ログを収集する方法について学習しました。 製品を Microsoft Sentinel に接続する方法の詳細については、次の記事を参照してください。

- [Syslog/CEF フォワーダーのデプロイ](connect-log-forwarder.md)
- [Microsoft Sentinel データ コネクタ リファレンス](data-connectors-reference.md)
- [ログ フォワーダーの接続に関するトラブルシューティング](troubleshooting-cef-syslog.md#validate-cef-connectivity)

Microsoft Sentinel で収集したデータの処理に関する詳細については、次の記事を参照してください。

- [CEF および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)についての説明。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。

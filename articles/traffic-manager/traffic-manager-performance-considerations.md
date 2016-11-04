---
title: Azure Traffic Manager のパフォーマンスに関する考慮事項 | Microsoft Docs
description: Traffic Manager でのパフォーマンス、および Traffic Manager を使用したときの Web サイトのパフォーマンスをテストする方法について説明します。
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: joaoma

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2016
ms.author: sewhee

---
# Traffic Manager のパフォーマンスに関する考慮事項
このページでは、Traffic Manager を使用する場合のパフォーマンスに関する考慮事項について説明します。考えられるシナリオ: 米国リージョンとアジアに Web サイトが 1 つずつあり、それらのいずれかが Traffic Manager プローブの正常性チェックに失敗し、すべてのユーザーが正常なリージョンにリダイレクトされます。この動作はパフォーマンスの問題があるように見えますが、ユーザー要求の距離に基づいて予想される動作になります。

## Traffic Manager の動作に関する重要な注意事項
* Traffic Manager は基本的に DNS の解決だけを行います。つまり、Traffic Manager が Web サイトに対して与える可能性があるパフォーマンスに関する唯一の影響は、初期 DNS 参照です。
* Traffic Manager の DNS 参照に関する説明のポイント。Traffic Manager は、ポリシーとプローブの結果に基づいて、通常の Microsoft DNS ルート サーバーを設定し、定期的に更新します。したがって、初期 DNS 参照の間であっても、DNS 要求は通常の Microsoft DNS ルート サーバーによって処理されるため、Traffic Manager による影響はありません。Traffic Manager が「停止」しても (つまり、ポリシーのプローブと DNS の更新を行っている VM での障害)、Microsoft DNS サーバーのエントリは維持されるため、Traffic Manager の DNS 名への影響はありません。唯一の影響は、ポリシーに基づくプローブと更新が行われないことです (つまり、プライマリ サイトがダウンした場合、Traffic Manager は DNS を更新してフェールオーバー サイトを参照できません)。
* トラフィックは Traffic Manager を通過しません。クライアントと Azure ホステッド サービスの間の仲介者として機能する Traffic Manager サーバーはありません。DNS 参照が終了すると、Traffic Manager はクライアントとサーバーの間の通信から完全に削除されます。
* DNS 参照は非常に高速で、キャッシュされます。初期 DNS 参照はクライアントとそれに構成されている DNS サーバーに依存し、通常、クライアントは DNS 参照を 50 ミリ秒以下で実行できます (http://www.solvedns.com/dns-comparison/ を参照)。最初の参照が終了すると、結果は DNS TTL の間キャッシュされます。Traffic Manager の場合、既定値は 300 秒です。
* ユーザーが選択した Traffic Manager のポリシー (パフォーマンス、フェールオーバー、ラウンド ロビン) は、DNS のパフォーマンスに影響を与えません。パフォーマンス ポリシーは、ユーザーのエクスペリエンスに悪影響を与える可能性があります。たとえば、米国のユーザーをアジアでホストされているサービスに送るような場合です。ただし、このようなパフォーマンスの問題は、Traffic Manager が原因ではありません。

## Traffic Manager のパフォーマンスのテスト
Traffic Manager のパフォーマンスと動作を調べるのに使用できる公開されている Web サイトがいくつかあります。これらのサイトは、DNS の待機時間を判定し、世界各地のユーザーが送られるホステッド サービスを特定するのに役立ちます。これらのツールのほとんどは DNS の結果をキャッシュしないので、テストを繰り返し実行してもそのたびに完全な DNS 参照が行われるのに対し、Traffic Manager エンドポイントに接続しているクライアントが完全な DNS 参照によるパフォーマンスの影響を受けるのは、TTL 期間中に 1 回だけであることに注意してください。

## パフォーマンスを測定するサンプル ツール
最も簡単なツールの 1 つは WebSitePulse です。URL を入力すると、DNS 解決時間、最初のバイト、最後のバイト、他のパフォーマンス統計などの統計情報が表示されます。サイトをテストするには、3 つの異なる場所から選択できます。この例の最初の実行では、最初の DNS 参照には 0.204 秒かかったことがわかります。同じ Traffic Manager エンドポイントで実行した 2 回目のテストでは、DNS 参照時間は 0.002 秒です。これは、結果が既にキャッシュされているためです。

http://www.websitepulse.com/help/tools.php

![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

キャッシュされているときの DNS 時間:

![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

複数の地理的な場所から同時に DNS 解決時間を取得するもう 1 つの本当に便利なツールは、Watchmouse の Check Website ツールです。URL を入力すると、DNS 解決時間、接続時間、および複数の地理的場所からの速度が表示されます。これはまた、Traffic Manager のパフォーマンス ポリシーをテストして、世界各地のユーザーが送られるホステッド サービスを確認するのにも便利です。

http://www.watchmouse.com/en/checkit.php

![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ – このツールは、Web サイトをテストして、ページ上の各要素のパフォーマンス統計情報をビジュアルなグラフで提供します。[Page Analysis] タブに切り替えると、DNS 参照を行うために使われた時間の割合を確認できます。

http://www.whatsmydns.net/ – このサイトは、20 の異なる地理的な場所から DNS 参照を実行して、結果を地図上に表示します。それを見ると、クライアントが接続されているホステッド サービスがわかります。

http://www.digwebinterface.com – watchmouse サイトと似ていますが、このサイトでは CNAME と A レコードを含む DNS のさらに詳細な情報が表示されます。オプションで [Colorize output] と [Stats] を選択し、[Nameservers] で [All] を選択してください。

## 次のステップ
[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-routing-methods.md)

[Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)

[Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager コマンドレット](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=AcomDC_0824_2016-->
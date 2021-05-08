---
title: Azure Communication Services 用に組織のネットワークを準備する
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services の音声およびビデオ通話に関するネットワーク要件を説明します
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108351"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Azure Communication Services で高品質なメディアを確保する

このドキュメントでは、Azure Communication Services を使用して高品質なマルチメディア コミュニケーション エクスペリエンスを構築する際に考慮すべき要素とベスト プラクティスについて、概要を説明します。

## <a name="factors-that-affect-media-quality-and-reliability"></a>メディアの品質と信頼性に影響する要素

Azure Communication Services のリアルタイム メディア (音声、ビデオ、アプリケーション共有) の品質には、さまざまな多数の要素が影響を及ぼします。 それらには、ネットワークの品質、帯域幅、ファイアウォール、ホスト、デバイス構成などがあります。


### <a name="network-quality"></a>ネットワークの品質

IP 経由のリアルタイム メディアの品質は、基になるネットワーク接続の品質、特に以下の量から大きな影響を受けます。
* **待機時間**。 これは、ネットワーク上の A 地点から B 地点に IP パケットが届くのにかかる時間です。 このネットワーク伝達がどれだけ遅延するかは、2 地点間の物理的な距離と、トラフィックが経由するデバイスで発生する追加のオーバーヘッドによって決まります。 待ち時間は、一方向の時間かラウンドトリップ時間 (RTT) として測定されます。
* **パケット損失**。 特定の時間枠において失われるパケットの割合。 パケット損失は音声の品質に直接的に影響します。個々のパケットが失われる小規模なケースではほとんど影響はありませんが、連続して爆発的な損失が発生すると音声が完全に途切れます。
* **パケット間の到着ジッターまたは単純なジッター**。 これは、連続するパケット間の遅延の平均的な変化です。 Azure Communication Services では、バッファーリングによって一定レベルのジッターに対応できます。 ジッターがバッファーリングを超過した場合にのみ、参加者はその効果に気付きます。

### <a name="network-bandwidth"></a>ネットワークの帯域幅

Azure Communication Services の同時メディア セッションとその他のビジネス アプリケーションに必要な帯域幅をサポートできるよう、ネットワークを確実に構成してください。 帯域幅のボトルネックがないかエンドツーエンドのネットワーク パスをテストすることが、Communication Services のマルチメディア ソリューションのデプロイを成功させるうえで重要です。

以下に示すのは、JavaScript SDK の帯域幅要件です。

|帯域幅|シナリオ|
|:--|:--|
|40 kbps|ピアツーピアの音声通話|
|500 kbps|ピアツーピアの音声通話と画面共有|
|500 kbps|360 p、30 fps のピアツーピア品質ビデオ通話|
|1.2 Mbps|720 p (HD 解像度)、30 fps のピアツーピア HD 品質ビデオ通話|
|500 kbps|360 p、30 fps のグループ ビデオ通話|
|1.2 Mbps|720 p (HD 解像度)、30 fps の HD グループ ビデオ通話| 

以下に示すのは、Android および iOS ネイティブ SDK の帯域幅要件です。

|帯域幅|シナリオ|
|:--|:--|
|30 kbps|ピアツーピアの音声通話 |
|130 kbps|ピアツーピアの音声通話と画面共有|
|500 kbps|360 p、30 fps のピアツーピア品質ビデオ通話|
|1.2 Mbps|720 p (HD 解像度)、30 fps のピアツーピア HD 品質ビデオ通話|
|1.5 Mbps|1080 p (HD 解像度)、30 fps のピアツーピア HD 品質ビデオ通話 |
|500 kbps および 1 Mbps|グループ ビデオ通話|
|1 Mbps および 2 Mbps|HD グループ ビデオ通話 (1080 p 画面上で 540 p のビデオ)|

### <a name="firewalls-configuration"></a>ファイアウォール構成

Azure Communication Services の接続では、高品質なマルチメディア エクスペリエンスを提供するために、特定のポートと IP アドレスへのインターネット接続が必要です。 これらのポートと IP アドレスにアクセスしなくても、Azure Communication Services は機能します。 ただし、最適なエクスペリエンスが実現するのは、推奨のポートと IP 範囲が開かれているときです。

| カテゴリ | IP 範囲または FQDN | Port | 
| :-- | :-- | :-- |
| メディア トラフィック | [Azure パブリック クラウド IP アドレスの範囲](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 から 3481、TCP ポート 443 |
| シグナリング、テレメトリ、登録| *.skype.com、*.microsoft.com、*.azure.net、*.azureedge.net、*.office.com、*.trouter.io | TCP 443、80 |

### <a name="network-optimization"></a>ネットワーク最適化

次のタスクは省略可能です。Azure Communication Services のロールアウトに必須ではありません。 このガイダンスは、ご自分のネットワークになんらかの制限があるとわかっている場合に、ネットワークと Azure Communication Services のパフォーマンスを最適化するために使用します。
さらに、以下の場合にも最適化が必要と考えられます。
* Azure Communication Services の動作が遅い (おそらく帯域幅が不十分です)
* 通話がつながらない (ファイアウォールまたはプロキシのブロッカーが原因の可能性があります)
* 通話が雑音交じりで、途切れる。または、音声がロボットのように聞こえる (ジッターまたはパケット損失が考えられます)

| ネットワーク最適化タスク | 詳細 |
| :-- | :-- |
| ネットワークを計画する | このドキュメントには、通話用のネットワークに関する最小要件が記載されています。 [ネットワークを計画するための Teams サンプル](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example)を参照してください |
| 外部名前解決 | Azure Communications Services SDK を実行しているすべてのコンピューターが、Azure Communication Services によって提供されるサービスを検出するために外部 DNS クエリを解決できるようにするほか、アクセスがファイアウォールによって妨げられていることのないようにします。 SDK が *.skype.com、*.microsoft.com、*.azure.net、*.azureedge.net、*.office.com、*.trouter.io のアドレスを解決できることを確認してください  |
| セッション永続化の維持 | UDP 用のマップ済みネットワーク アドレス変換 (NAT) アドレスまたはポートが、ファイアウォールによって変更されないようにします
NAT プール サイズの検証 | ユーザーの接続に必要なネットワーク アドレス変換 (NAT) プール サイズを検証します。 複数のユーザーとデバイスが[ネットワーク アドレス変換 (NAT) またはポート アドレス変換 (PAT)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365) を使用して Azure Communication Services にアクセスする場合は、パブリックにルーティング可能な各 IP アドレスの背後に隠されているデバイスが、サポートされる数を超過しないようにします。 ポート不足を防ぐために、適切なパブリック IP アドレスが NAT プールに割り当てられるようにしてください。 ポート不足は、内部のユーザーとデバイスが Azure Communication Services に接続できなくなる原因となります |
| 侵入の検出と防止に関するガイダンス | アウトバウンド接続にセキュリティ レイヤーを追加するために[侵入検出](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)または防止システム (IDS または IPS) が自分の環境にデプロイされている場合は、Azure Communication Services の URL をすべて許可します |
| 分割トンネル VPN の構成 | 仮想プライベート ネットワーク (VPN) をバイパスする Teams トラフィックに代替パスを用意することをお勧めします。これは一般に[分割トンネル VPN](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing) と呼ばれます。 分割トンネリングでは、Azure Communications Services のトラフィックが VPN を経由せず、Azure に直接送信されます。 VPN をバイパスすると、メディアの品質に良い影響があり、VPN デバイスと組織のネットワークの負荷が減少します。 分割トンネル VPN を実装する際は、お客様の VPN ベンダーと協力してください。 VPN のバイパスが推奨されるその他の理由は以下のとおりです。 <ul><li> VPN は通常、リアルタイム メディアをサポートする設計または構成になっていません。</li><li> また VPN では、(Azure Communication Services に必要である) UDP がサポートされていない可能性があります</li><li>さらに VPN では、既に暗号化されているメディア トラフィックの上に追加の暗号化レイヤーを導入します。</li><li>VPN デバイス経由のヘアピン トラフィックが原因で、Azure Communication Services への接続が効率的でない可能性があります。</li></ul>|
| QoS の実装 | [サービスの品質 (QoS) を使用](https://docs.microsoft.com/microsoftteams/qos-in-teams)して、パケットの優先順位付けを構成します。 これにより、通話の品質が向上するほか、通話の品質の監視とトラブルシューティングが行いやすくなります。 QoS は、マネージド ネットワークのすべてのセグメントで実装される必要があります。 ネットワークが適切にプロビジョニングされ帯域幅が確保されている場合も、予定外のネットワーク イベントが発生した際に QoS によってリスクが緩和されます。 QoS を使用すると、そういった予定外のイベントが品質に悪影響を及ぼすことのないように、音声トラフィックが優先順位付けされます。 | 
| WiFi の最適化 | VPN と同様に、WiFi ネットワークは必ずしもリアルタイム メディアをサポートするように設計または構成されていません。 Azure Communication Services をサポートするよう WiFi ネットワークを計画 (または最適化) することは、高品質なデプロイを実現するうえで重要な考慮事項です。 次の点を考慮します。 <ul><li>QoS または WiFi Multimedia (WMM) を実装して、メディア トラフィックが WiFi ネットワーク経由で適切に優先順位付けされるようにします。</li><li>WiFi バンドとアクセス ポイントの場所を計画および最適化します。 2\.4 GHz 帯でもアクセス ポイントの配置によっては適切なエクスペリエンスを実現できるかもしれませんが、アクセス ポイントは、この帯で動作する他のコンシューマー デバイスの影響を頻繁に受けます。 5 GHz 帯の方が密度が高いため、リアルタイム メディアに適しています。ただし、対象範囲を十分なものにするためにアクセス ポイントを増やす必要があります。 さらに、エンドポイントもこの帯をサポートする必要があります。そのため、これらの帯を利用するよう構成します。</li><li>デュアルバンド WiFi ネットワークを使用している場合は、バンド ステアリングの実装を検討します。 バンド ステアリングは、デュアルバンドのお客様が 5 GHz 帯を使用するようにする技術で、WiFi ベンダーによって実装されます。</li><li>同じチャネルのアクセス ポイントが互いに近すぎると、信号の重複と意図しない競合が発生し、結果としてユーザー エクスペリエンスが低下するおそれがあります。 互いに隣り合うアクセス ポイントが、重複しないチャネルに配置されるようにします。</li></ul> ワイヤレス ベンダー各社は、そのワイヤレス ソリューションをデプロイするうえで独自の推奨事項を持っています。 固有のガイダンスについては、ご自分の WiFi ベンダーにご相談ください。|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>オペレーティング システムとブラウザー (JavaScript SDK 向け)

Azure Communication Services の音声およびビデオ SDK では、特定のオペレーティング システムとブラウザーがサポートされます。
通話 SDK でサポートされるオペレーティング システムとブラウザーについては、[通話の概念説明ドキュメント](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)を参照してください。

## <a name="next-steps"></a>次のステップ

次のドキュメントもご覧ください。

- [通話ライブラリ](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)について学習する
- [クライアント - サーバー アーキテクチャ](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)について学習する
- [通話フローのトポロジ](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)について学習する

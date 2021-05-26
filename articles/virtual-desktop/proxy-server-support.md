---
title: Windows Virtual Desktop のプロキシ サーバーのガイドライン - Azure
description: Windows Virtual Desktop のデプロイでプロキシ サーバーを使用するためのガイドラインと推奨事項について説明します。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
ms.reviewer: denisgun
manager: femila
ms.openlocfilehash: 7928c86375eb66a6518326bfcadfb1278193cfc3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454073"
---
# <a name="proxy-server-guidelines-for-windows-virtual-desktop"></a>Windows 仮想デスクトップのプロキシ サーバーのガイドライン

この記事では、Windows 仮想デスクトップでプロキシ サーバーを使用する方法について説明します。 この記事の推奨事項は、Windows Virtual Desktop インフラストラクチャ、クライアント、およびセッション ホスト エージェント間の接続にのみ適用されます。 この記事では、Office、Windows 10、FSLogix、またはその他の Microsoft アプリケーションのネットワーク接続については説明しません。

## <a name="what-are-proxy-servers"></a>プロキシ サーバーとは

Windows Virtual Desktop トラフィックのプロキシをバイパスすることをお勧めします。 Windows Virtual Desktop のトラフィックは既に暗号化されているため、プロキシによってセキュリティが強化されることはありません。 接続のセキュリティの詳細については、「[接続のセキュリティ](network-connectivity.md#connection-security)」を参照してください。 

ほとんどのプロキシ サーバーは、長時間実行される WebSocket 接続をサポートするように設計されていないため、接続の安定性に影響する可能性があります。 Windows Virtual Desktop では複数の長時間の接続が使用されるため、プロキシ サーバーのスケーラビリティでも問題が発生します。 プロキシ サーバーを使用する場合は、これらの接続を実行できるような適切なサイズになっている必要があります。

プロキシ サーバーの地理的な場所がホストから遠く離れている場合、この距離が原因でユーザー接続の待機時間が長くなります。 待機時間が長くなるほど、接続速度が遅くなり、特に、グラフィックス、オーディオ、または入力デバイスとの待機時間の短い通信が必要なシナリオでユーザー エクスペリエンスが低下します。 プロキシ サーバーを使用する必要がある場合は、Windows Virtual Desktop エージェントおよびクライアントと同じ地理的な場所にサーバーを配置する必要があることに注意してください。

Windows Virtual Desktop トラフィックが通る唯一のパスとしてプロキシ サーバーを構成すると、リモート デスクトップ プロトコル (RDP) データは、ユーザー データグラム プロトコル (UDP) ではなく伝送制御プロトコル (TCP) を介して強制的に送信されます。 この移動により、リモート接続の視覚的な品質と応答性が低下します。

まとめとして、Windows Virtual Desktop ではプロキシ サーバーを使用しないことをお勧めします。これは、待機時間の増加やパケット損失によるパフォーマンス関連の問題を引き起こすためです。 

## <a name="bypassing-a-proxy-server"></a>プロキシ サーバーのバイパス

組織のネットワーク ポリシーとセキュリティ ポリシーで Web トラフィックのプロキシ サーバーが必須になっている場合は、プロキシ サーバー経由でトラフィックをルーティングしながら、Windows Virtual Desktop 接続をバイパスするように環境を構成できます。 ただし、各組織に固有のポリシーがあるため、一部の方法の方が他の方法よりも展開に適している場合があります。 以下に環境でのパフォーマンスと信頼性の低下を防ぐために試すことができるいくつかの構成方法を示します。

- Azure ファイアウォールでの Azure サービス タグ
- プロキシ自動構成 (PAC) ファイルを使用してプロキシ サーバーをバイパスする
- ローカル プロキシ構成のリストをバイパスする 
- ユーザー構成ごとにプロキシ サーバー使用する 
- プロキシ経由のサービス トラフィックを保持しながら RDP 接続に RDP Shortpath を使用する 

## <a name="recommendations-for-using-proxy-servers"></a>プロキシ サーバーを使用するための推奨事項

一部の組織では、追跡またはパケット検査のためにすべてのユーザー トラフィックが、プロキシ サーバーを経由する必要があります。 このセクションでは、このような場合に推奨される環境の構成方法について説明します。

### <a name="use-proxy-servers-in-the-same-azure-geography"></a>同じ Azure 地域でプロキシ サーバーを使用する

プロキシ サーバーを使用すると、Windows Virtual Desktop インフラストラクチャとのすべての通信が処理され、最も近い Azure Front Door に対する DNS 解決とエニーキャスト ルーティングが実行されます。 プロキシ サーバーが Azure 地域で遠い場所にある場合や分散している場合は、地理的な解決の精度が低下します。 地理的な解決の精度の低下は、接続がより遠くの Windows Virtual Desktop クラスターにルーティングされることを意味します。 この問題を回避するには、Windows Virtual Desktop クラスターに地理的に近い場所にあるプロキシ サーバーのみを使用します。

### <a name="use-rdp-shortpath-for-desktop-connectivity"></a>デスクトップ接続に RDP Shortpath を使用する

RDP Shortpath を有効にすると、可能な場合に RDP データがプロキシ サーバーをバイパスします。 プロキシ サーバーをバイパスすると、UDP トランスポートを使用しながら最適なルーティングを実現できます。 その他の Windows Virtual Desktop トラフィック (ブローカー、オーケストレーション、診断など) は、引き続きプロキシ サーバーを経由します。 

### <a name="dont-use-ssl-termination-on-the-proxy-server"></a>プロキシ サーバーで SSL ターミネーションを使用しない

Secure Sockets Layer (SSL) ターミネーションでは、Windows Virtual Desktop コンポーネントのセキュリティ証明書が、プロキシ サーバーによって生成された証明書に置き換えられます。 このプロキシ サーバー機能を使用すると、プロキシ サーバーでの HTTPS トラフィックのパケット検査が有効になります。 ただし、パケット検査によってサービスの応答時間が長くなり、ユーザーがサインインするまでに時間がかかることがあります。 リバース接続のシナリオでは、リバース接続の RDP トラフィックはバイナリであり、追加のレベルの暗号化を使用するため、RDP トラフィックのパケット検査は必要ありません。

SSL 検査を使用するようにプロキシ サーバーを構成する場合は、SSL 検査によって変更が加えられた後に、サーバーを元の状態に戻すことはできないことに注意してください。 SSL 検査が有効になっている間に Windows Virtual Desktop 環境内で何かの機能が停止した場合は、サポート ケースを開く前に、SSL 検査を無効にしてもう一度試す必要があります。 また、SSL 検査では、Windows Virtual Desktop エージェントによってエージェントとサービスの間の信頼関係接続が妨げられるため、エージェントの動作が停止することがあります。

### <a name="dont-use-proxy-servers-that-need-authentication"></a>認証を必要とするプロキシ サーバーを使用しない

セッション ホスト上の Windows Virtual Desktop コンポーネントは、オペレーティング システムのコンテキストで実行されるので、認証を必要とするプロキシ サーバーをサポートしません。 プロキシ サーバーに認証が必要な場合、接続は失敗します。

### <a name="plan-for-the-proxy-server-network-capacity"></a>プロキシ サーバーのネットワーク容量を計画する

プロキシ サーバーには容量の制限があります。 通常の HTTP トラフィックとは異なり、RDP トラフィックは、長時間実行され、双方向で大量の帯域幅を消費する煩雑な接続です。 プロキシ サーバーを設定する前に、プロキシ サーバーのベンダーに問い合わせて、サーバーのスループットを確認してください。 また、一度に実行できるプロキシ セッションの数を必ず確認してください。 プロキシ サーバーを展開した後、Windows Virtual Desktop トラフィックのボトルネックに関して、サーバーによるリソースの使用を注意深く監視してください。

### <a name="proxy-servers-for-windows-7-session-hosts"></a>Windows 7 セッション ホスト用のプロキシ サーバー

Windows 7 で実行されているセッション ホストは、リバース接続 RDP データ用のプロキシ サーバー接続をサポートしていません。 セッション ホストが Windows Virtual Desktop ゲートウェイに直接接続できない場合、接続は機能しません。

### <a name="proxy-servers-and--teams-optimization"></a>プロキシ サーバーと Teams の最適化

Windows Virtual Desktop は、Teams 最適化のためのプロキシ サーバーをサポートしていません。

## <a name="session-host-configuration-recommendations"></a>セッション ホストの構成に関する推奨事項

セッション ホスト レベルのプロキシ サーバーを構成するには、システム全体のプロキシを有効にする必要があります。 システム全体の構成は、セッション ホストで実行されているすべての OS コンポーネントとアプリケーションに影響することに注意してください。 次のセクションでは、システム全体のプロキシを構成するための推奨事項を示します。
 
### <a name="use-the-web-proxy-auto-discovery-wpad-protocol"></a>Web プロキシ自動検出 (WPAD) プロトコルを使用する

Windows Virtual Desktop エージェントは、Web プロキシ自動検出 (WPAD) プロトコルを使用して、ネットワーク上のプロキシ サーバーを自動的に検出しようとします。 位置情報の試行中に、エージェントはドメイン ネーム サーバー (DNS) で **wpad. domainsuffix** という名前のファイルを検索します。 エージェントが DNS 内でこのファイルを検出した場合、**wpad.dat** という名前のファイルに対して HTTP 要求を行います。 応答は、送信プロキシ サーバーを選択するプロキシ構成スクリプトになります。

WPAD 用に DNS 解決を使用するようにネットワークを構成するには、[Internet Explorer 11 の自動検出の設定](/internet-explorer/ie11-deploy-guide/auto-detect-settings-for-ie11)に関する記事の手順に従います。 「[Set-DnsServerGlobalQueryBlockList](/powershell/module/dnsserver/set-dnsserverglobalqueryblocklist?view=windowsserver2019-ps&preserve-view=true)」の指示に従って、DNS サーバーのグローバル クエリ ブロックリストで WPAD 解決が許可されていることを確認してください。

### <a name="manually-set-a-device-wide-internet-explorer-proxy"></a>デバイス全体の Internet Explorer プロキシを手動で設定する

[Network Proxy CSP](/windows/client-management/mdm/networkproxy-csp) を使用して、すべての対話型、LocalSystem、NetworkService ユーザーに適用されるデバイス全体のプロキシまたはプロキシ自動構成 (.PAC) ファイルを設定できます。 

次の例に示すように、次の **bitsadmin** コマンドを実行して、ローカル システム アカウントのプロキシ サーバーを構成することもできます。 

```console
bitsadmin /util /setieproxy LOCALSYSTEM AUTOSCRIPT http://server/proxy.pac 
```

## <a name="client-side-proxy-support"></a>クライアント側プロキシのサポート

Windows Virtual Desktop クライアントでは、システム設定または[ネットワーク プロキシ CSP](/windows/client-management/mdm/networkproxy-csp) を使用して構成されたプロキシ サーバーがサポートされます。

### <a name="support-for-clients-running-on-windows-7"></a>Windows 7 で実行されているクライアントのサポート

Windows 7 で実行されているクライアントは、リバース接続 RDP データ用のプロキシ サーバー接続をサポートしていません。 クライアントから Windows Virtual Desktop ゲートウェイに直接接続できない場合、接続は機能しません。

### <a name="windows-virtual-desktop-client-support"></a>Windows Virtual Desktop クライアントのサポート

次の表は、プロキシ サーバーをサポートする Windows Virtual Desktop クライアントを示しています。

| クライアント名 | プロキシ サーバーのサポート |
|---|---|
| Windows デスクトップ | はい |
| Web クライアント | はい |
| Android | いいえ |
| iOS | はい |
| macOS | はい |
| Windows ストア | はい |

Linux ベースのシン クライアントでのプロキシ サポートの詳細については、「[シン クライアントのサポート](linux-overview.md)」を参照してください。

## <a name="support-limitations"></a>サポートの制限

プロキシ サーバーとして機能するサードパーティのサービスやアプリケーションは数多くあります。 これらのサードパーティ サービスには、分散された次世代ファイアウォール、Web セキュリティ システム、および基本プロキシ サーバーが含まれます。 すべての構成が Windows 仮想デスクトップと互換性があることを保証することはできません。 Microsoft では、プロキシ サーバーで確立された接続に対して制限されたサポートのみを提供しています。 Microsoft サポートでは、プロキシ サーバーの使用中に接続の問題が発生した場合は、プロキシ バイパスを構成してから、問題の再現を試みることをお勧めします。

## <a name="next-steps"></a>次のステップ

Windows Virtual Desktop の展開のセキュリティを維持する方法の詳細については、[セキュリティ ガイド](security-guide.md)に関するページを参照してください。

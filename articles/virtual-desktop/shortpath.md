---
title: Azure Virtual Desktop マネージド ネットワーク用 RDP Shortpath
titleSuffix: Azure
description: Azure Virtual Desktop にマネージド ネットワーク用 RDP Shortpath を設定する方法。
author: gundarev
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: denisgun
ms.openlocfilehash: b6f41f52b224603d32586ca6e260eaae54466f80
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709325"
---
# <a name="azure-virtual-desktop-rdp-shortpath-for-managed-networks"></a>Azure Virtual Desktop マネージド ネットワーク用 RDP Shortpath

マネージド ネットワーク用 RDP Shortpath は、リモート デスクトップ クライアントとセッション ホストとの間に直接的な UDP ベースのトランスポートを確立する Azure Virtual Desktop の機能です。 RDP では、このトランスポートを使用して、より高い信頼性と一貫した待機時間で、リモート デスクトップと RemoteApp を提供します。

## <a name="key-benefits"></a>主な利点

* RDP Shortpath トランスポートは、非常に効率的な [Universal Rate Control Protocol (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/) に基づいています。 URCP では、ネットワークの状態をアクティブに監視することで UDP を強化し、公平で完全なリンク使用率を実現します。 URCP は、リモート デスクトップの必要に応じて、低遅延および低損失レベルで動作します。 URCP を使用すると、ネットワーク パラメーターを動的に学習し、レート制御メカニズムを備えたプロトコルを提供することにより、最高のパフォーマンスが達成されます。
* RDP Shortpath は、リモート デスクトップ クライアントとセッション ホストとの間に直接接続を確立します。 直接接続により、Azure Virtual Desktop ゲートウェイ上の依存関係が軽減され、接続の信頼性が向上し、各ユーザー セッションに使用できる帯域幅が増加します。
* 余分なリレーを削除すると、ラウンド トリップ時間が短縮され、待機時間の影響を受けやすいアプリケーションと入力方式のユーザー エクスペリエンスが向上します。
* RDP Shortpath では、DSCP (Differentiated Services Code Point) マークを使用した RDP 接続の [サービスの品質 (QoS) 優先度の構成](./rdp-quality-of-service-qos.md)がサポートされます
* RDP Shortpath トランスポートを使用することにより、各セッションのスロットル率を指定することで、[送信ネットワーク トラフィックを制限する](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate)ことができます。

## <a name="connection-security"></a>接続のセキュリティ

RDP Shortpath により、RDP マルチトランスポート機能が拡張されます。 逆方向接続トランスポートは置き換えられませんが、補完されます。 初期セッション ブローカリングはすべて、Azure Virtual Desktop インフラストラクチャを介して管理されます。

デプロイでは、リバース接続トランスポートで認証された着信 Shortpath トラフィックに対して、ユーザーが構成した UDP ポートのみが使用されます。 RDP Shortpath リスナーでは、リバース接続セッションと一致するものを除き、接続試行はすべて無視されます。

RDP Shortpath では、セッション ホストの証明書を使用して、クライアントとセッション ホスト間の TLS 接続を使用します。 既定では、RDP 暗号化に使用される証明書は、デプロイ中に OS によって自己生成されます。 必要に応じて、顧客は企業の証明機関によって発行され、一元管理された証明書を展開できます。 証明書の構成の詳細については、[Windows Server ドキュメント](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)を参照してください。

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath の接続シーケンス

[リバース接続トランスポート](./network-connectivity.md)を確立すると、クライアントとセッション ホストによって RDP 接続が開始され、マルチトランスポート機能がネゴシエートされます。

セッション ホストによってマルチトランスポート機能がネゴシエートされる方法を次に示します。

1. セッション ホストにより、プライベートおよびパブリックの IPv4 と IPv6 のアドレスの一覧がクライアントに送信されます。
2. クライアントでは、バックグラウンド スレッドが開始され、ホストの IP アドレスの 1 つに対して、UDP ベースの並列トランスポートが直接確立されます。
3. クライアントは、指定された IP アドレスを調査している間、逆方向接続トランスポートを介して初期接続の確立を続行し、ユーザー接続の遅延が発生しないようにします。
4. クライアントに直接の通信経路がある場合、クライアントによって、そのセッション ホストとの安全な TLS 接続が確立されます。
5. Shortpath トランスポートを確立すると、RDP によってリモート グラフィックス、入力、デバイス リダイレクトを含むすべての動的仮想チャネル (DVC) が新しいトランスポートに移動されます。
6. ファイアウォールまたはネットワーク トポロジにより、クライアントが直接 UDP 接続を確立できない場合、RDP は逆方向接続トランスポートを続行します。

次の図は、RDP Shortpath ネットワーク接続の概要を示しています。

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP Shortpath ネットワーク接続の図" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>必要条件

RDP Shortpath をサポートするには、Azure Virtual Desktop クライアントに、セッション ホストへの直接の通信経路が必要です。 直接の通信経路を取得するには、これらのいずれかの方法を使用します。

- リモート クライアント コンピューターで Windows 10 または Windows 7 のいずれかが実行されていて、[Windows デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/windowsdesktop)がインストールされていることを確認する。 現時点では、Windows 以外のクライアントはサポートされていません。
- [ExpressRoute プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md)を使用する
- [サイト間仮想プライベート ネットワーク (VPN) (IPsec ベース)](../vpn-gateway/tutorial-site-to-site-portal.md) を使用する
- [ポイント対サイト VPN (IPsec ベース)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) を使用する
- [パブリック IP アドレスの割り当て](../virtual-network/ip-services/virtual-network-public-ip-address.md)を使用する

他の VPN の種類を使用して Azure に接続する場合は、ユーザー データグラム プロトコル (UDP) ベースの VPN を使用することをお勧めします。 ほとんどの伝送制御プロトコル (TCP) ベースの VPN ソリューションでは、入れ子になった UDP がサポートされていますが、TCP 輻輳制御の継承されたオーバーヘッドが追加されるため、RDP のパフォーマンスが低下します。

直接の通信経路を持つことは、クライアントがファイアウォールにブロックされることなく、セッション ホストに直接接続できることを意味します。

## <a name="configure-rdp-shortpath-for-managed-networks"></a>マネージド ネットワーク用 RDP Shortpath を構成する

マネージド ネットワーク用 RDP Shortpath を有効にするには、セッション ホストで RDP Shortpath リスナーを有効にする必要があります。 ご利用の環境内で使用されている任意の数のセッション ホストで RDP Shortpath を有効にすることができます。 ただし、ホスト プール内のすべてのホストで RDP Shortpath を有効にする必要はありません。

RDP Shortpath リスナーを有効にするには、次のようにします。

1. まず、Azure Virtual Desktop の規則と設定を追加する管理用テンプレートをインストールします。 [Azure Virtual Desktop ポリシー テンプレート ファイル](https://aka.ms/avdgpo) (AVDGPTemplate.cab) をダウンロードして、.cab ファイルと .zip アーカイブの内容を抽出します。
2. **terminalserver-avd.admx** ファイルをコピーして、それを **%windir%\PolicyDefinitions** フォルダーに貼り付けます。
3. **en-us\terminalserver-avd.adml** ファイルをコピーして、それを **%windir%\PolicyDefinitions\en-us** フォルダーに貼り付けます。
4. ファイルが正しくコピーされたかを確認するために、**グループ ポリシー エディター** を開き、**[コンピューターの構成]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[リモート デスクトップ サービス]** > **[リモート デスクトップ セッション ホスト]** > **[Azure Virtual Desktop]** の順に移動します。
5. 次のスクリーンショットに示されているように、1 つまたは複数の Azure Virtual Desktop ポリシーが表示されます

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="グループ ポリシー エディターのスクリーンショット" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!NOTE]
   > Active Directory ドメインのグループ ポリシー セントラル ストアに管理用テンプレートをインストールすることもできます。
   > グループ ポリシー管理用テンプレートのセントラル ストアについて詳しくは、「[Windows でグループ ポリシー管理用テンプレート用のセントラル ストアを作成および管理する方法](/troubleshoot/windows-client/group-policy/create-and-manage-central-store)」を参照してください。

6. **[Enable RDP Shortpath for managed networks]\(マネージド ネットワーク用 RDP Shortpath を有効にする\)** ポリシーを開き、**"有効"** に設定します。 このポリシー設定を有効にすると、Azure Virtual Desktop セッション ホストが着信接続をリッスンするために使用するポート番号を構成することもできます。 既定のポートは 3390 です。
7. 変更を適用するために、セッション ホストを再起動します。

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>[セキュリティが強化された Windows ファイアウォール] を構成する

RDP Shortpath の受信ネットワーク トラフィックを許可するには、グループ ポリシー管理 MMC スナップインの [セキュリティが強化された Windows Defender ファイアウォール] ノードを使用して、ファイアウォール規則を作成します。

1. [セキュリティが強化された Windows Defender ファイアウォール](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)のグループ ポリシー管理コンソールを開きます。
2. ナビゲーション ウィンドウで、 **[受信規則]** をクリックします。
3. **[操作]** 、 **[新しい規則]** の順に選択します。
4. 新規の受信の規則ウィザードの **[規則の種類]** ページで、 **[カスタム]** 、 **[次へ]** の順に選択します。
5. **[プログラム]** ページで、 **[このプログラムのパス]** を選択し、「%SystemRoot%\system32\svchost.exe」と入力してから、 **[次へ]** を選択します。
6. **[プロトコルおよびポート]** ページで、UDP プロトコルの種類を選択します。 **[ローカル ポート]** で、[特定のポート] を選択し、構成済みの UDP ポートを入力します。 既定の設定のままにした場合、ポート番号は 3390 になります。
7. [**スコープ**] ページでは、このページで入力した IP アドレスに対する送受信ネットワーク トラフィックにのみ規則が適用されるよう指定できます。 設計に適した構成を行い、 **[次へ]** を選択します。
8. **[操作]** ページで、 **[接続を許可する]** を選択し、 **[次へ]** を選択します。
9. **[プロファイル]** ページで、この規則を適用するネットワークの場所の種類を選択し、 **[次へ]** をクリックします。
10. **[名前]** ページで、規則の名前と説明を入力して、**[完了]** を選択します。

完了したら、新しい規則が次のスクリーンショットの形式と一致していることを確認します。
:::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="RDP Shortpath ネットワーク接続のファイアウォールを構成するための [全般] タブのスクリーンショット。[接続を許可する] オプションが選択されています" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="RDP Shortpath ネットワーク接続のファイアウォールを構成するための [プログラムおよびサービス] タブのスクリーンショット。[リモート デスクトップ サービス] が選択されています" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="RDP Shortpath ネットワーク接続のファイアウォールを構成するための [プロトコルおよびポート] タブのスクリーンショット。UDP ポート 3390 が構成されています" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

また、PowerShell を使用して Windows ファイアウォールを構成することができます。

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>PowerShell を使用した Windows Defender ファイアウォールの構成

PowerShell を使用して、次のコマンドレットを実行してグループ ポリシーを構成することもできます。

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Azure ネットワーク セキュリティ グループの構成

ネットワーク セキュリティ境界を越えて RDP Shortpath リスナーへのアクセスを許可するには、Azure ネットワーク セキュリティ グループを構成して受信 UDP ポート 3390 を許可する必要があります。
[ネットワーク セキュリティ グループのドキュメント](../virtual-machines/windows/nsg-quickstart-portal.md)に従い、次のパラメーターを使用して、トラフィックを許可する受信セキュリティ規則を作成します。

* **Source** - **Any** またはクライアントが存在する IP 範囲
* **[ソース ポート範囲]**  -  **\***
* **[宛先]**  -  **[Any]**
* **Destination port ranges** - **3390**
* **Protocol** - **UDP**
* **Action** - **Allow**
* 必要に応じて、**Priority** を変更します。 優先度は、ルールが適用される順序に影響します。数値が小さいほど、ルールが早く適用されます。
* **Name** - - **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>特定のサブネットに対して RDP Shortpath を無効にする

特定のサブネットが RDP Shortpath トランスポートを使用できないようにする必要がある場合、正しい発信元 IP 範囲を指定する別のネットワーク セキュリティ グループを構成できます。

## <a name="verify-your-network-connectivity"></a>ネットワーク接続を確認します。

次に、ネットワークで RDP Shortpath が使用されていることを確認する必要があります。 これを行うには、[接続情報] ダイアログまたは Log Analytics のいずれかを使用します。

### <a name="connection-information-dialog"></a>[接続情報] ダイアログ

接続で RDP Shortpath が使用されていることを確認します。次のスクリーンショットに示されているように、画面の上部にある **接続** ツール バーに移動し、アンテナ アイコンを選択して [接続情報] ダイアログを開きます。

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="リモート デスクトップ接続バーの画像":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="[リモート デスクトップ接続情報] ダイアログの画像":::

### <a name="using-event-logs"></a>イベント ログの使用

セッションで RDP Shortpath トランスポートが使用されていることを確認するには、次のようにします。

1. 任意の Azure Virtual Desktop クライアントを使用して、VM デスクトップに接続します。
2. **イベント ビューアー** を開き、**[アプリケーションとサービス ログ]** > **[Microsoft]** > **[Windows]** > **[RemoteDesktopServices-RdpCoreCDV]** > **[Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational]** の順に移動します。
3. イベント ID 131 が表示されたら、ネットワークで RDP Shortpath トランスポートが使用されています。

### <a name="use-log-analytics"></a>Log Analytics の使用

[Azure Log Analytics](./diagnostics-log-analytics.md) を使用している場合、[WVDConnections テーブル](/azure/azure-monitor/reference/tables/wvdconnections)に対してクエリを実行することで接続を監視できます。 UdpUse という名前の列は、Azure Virtual Desktop RDP スタックが現在のユーザー接続で UDP プロトコルを使用しているかどうかを示します。
設定できる値は次のとおりです。

* **0** - ユーザー接続で RDP Shortpath は使用されていない
- **1** - ユーザー接続でマネージド ネットワーク用 RDP Shortpath が使用されている
  
次のクエリ一覧では、接続情報を確認できます。 このクエリは、[Log Analytics クエリ エディター](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)で実行できます。 クエリごとに、`userupn` を検索するユーザーの UPN に置き換えます。

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="verify-shortpath-listener"></a>Shortpath リスナーを確認する

UDP リスナーが有効であることを確認するには、セッション ホストで次の PowerShell コマンドを使用します。

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

有効な場合、次のような出力が表示されます

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

競合がある場合は、次のコマンドを実行して、ポートをブロックしているプロセスを特定できます。

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP Shortpath を無効にする

場合によっては、RDP Shortpath トランスポートを無効にする必要があります。 RDP Shortpath は、グループ ポリシーを使用して無効にすることができます。

### <a name="disabling-rdp-shortpath-on-the-client"></a>クライアントで RDP Shortpath を無効にする

特定のクライアントに対して RDP Shortpath を無効にするには、次のグループ ポリシーを使用して UDP のサポートを無効にすることができます。

1. クライアントで、**gpedit.msc** を実行します。
2. **[コンピューターの構成]** > **[管理用テンプレート]** > **[Windows コンポーネント]** > **[リモート デスクトップ サービス]** > **[リモート デスクトップ接続のクライアント]** の順に移動します。
3. **[クライアントの UDP を無効にする]** 設定を **[有効]** に設定します

### <a name="disable-rdp-shortpath-on-the-session-host"></a>セッション ホストで RDP Shortpath を無効にする

特定のセッション ホストに対して RDP Shortpath を無効にするには、次のグループ ポリシーを使用して UDP のサポートを無効にすることができます。

1. セッション ホストで **gpedit.msc** を実行します。
2. **[コンピューターの構成] > [管理テンプレート] > [Windows コンポーネント] > [リモート デスクトップ サービス] > [リモート デスクトップ セッション ホスト] > [接続]** の順に移動します。
3. **[RDP トランスポート プロトコルの選択]** 設定を **[TCP のみ]** に設定します。

## <a name="next-steps"></a>次のステップ

* Azure Virtual Desktop のネットワーク接続の詳細については、「[Azure Virtual Desktop のネットワーク接続について](network-connectivity.md)」を参照してください。
* Azure Virtual Desktop 用サービス品質 (QoS) の使用を開始するには、[Azure Virtual Desktop 用サービス品質 (QoS) の実装](rdp-quality-of-service-qos.md)に関するページをご覧ください。

## サービス チェイニング - ピアリングされた VNet 経由のトランジット

システム ルートを使用するとデプロイのトラフィックが自動的に促進されますが、仮想アプライアンスを通過するパケットのルーティングを自分で制御したい場合もあります。このシナリオでは、以下の図に示したように、HubVNet と VNet1 という 2 つの VNet がサブスクリプションに存在します。ネットワーク仮想アプライアンス (NVA) は、VNet HubVNet にデプロイします。HubVNet と VNet1 との間で VNet ピアリングを確立したら、ユーザー定義ルートを設定し、次ホップを HubVNet 内の NVA に指定できます。

![NVA Transit](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] ここでは単純化するために、すべての VNet が同じサブスクリプションに存在することを前提としています。ただし、異なるサブスクリプション間のシナリオでも正常に機能します。

トランジット ルーティングの実現の鍵となるプロパティは、"Allow Forwarded Traffic" パラメーターです。この設定によって、ピアリングされた VNet 内の NVA からトラフィックを受信したり、その NVA にトラフィックを送信したりすることができます。

<!---HONumber=AcomDC_0928_2016-->
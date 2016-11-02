仮想ネットワーク ゲートウェイを作成する場合、使用するゲートウェイの SKU を指定する必要があります。 選択するゲートウェイ SKU が上位になるほど、ゲートウェイに割り当てられる CPU やネットワーク帯域幅が増えます。その結果、ゲートウェイで仮想ネットワークに対してより高いネットワーク スループットをサポートできます。

VPN Gateway で利用できる SKU は次のとおりです。

- 基本
- Standard
- HighPerformance

SKU の選択にあたっては、以下の制限事項を考慮してください。

- PolicyBased VPN タイプを使用する場合には、Basic ゲートウェイの SKU を使用する必要があります。 PolicyBased VPN (旧称静的ルーティング) は、その他の SKU ではサポートされていません。
- BGP は、Basic SKU ではサポートされていません。
- ExpressRoute と VPN Gateway が共存する構成は、Basic SKU ではサポートされていません。


<!--HONumber=Oct16_HO2-->



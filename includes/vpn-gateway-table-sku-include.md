次の 3 つの VPN Gateway の SKU があります。

* Basic
* Standard
* 高性能

次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。料金はゲートウェイの SKU によって異なります。料金については、「[VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)」を参照してください。この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

|  | **VPN Gateway のスループット** | **VPN Gateway の IPsec トンネルの最大数** | **ExpressRoute ゲートウェイのスループット** | **VPN Gateway と ExpressRoute の共存** |
| --- | --- | --- | --- | --- |
| **Basic SKU** |100 Mbps |10 |500 Mbps |いいえ |
| **Standard SKU** |100 Mbps |10 |1000 Mbps |あり |
| **高性能 SKU** |200 Mbps |30 |2000 Mbps |あり |

**注:** VPN のスループットは、同一 Azure リージョン内の VNet 間での測定値に基づく大まかな推定値です。インターネット経由でのクロスプレミス接続ではこの値は達成可能な最大値であり、このスループットが保証されるわけではありません。

<!---HONumber=AcomDC_0224_2016-->
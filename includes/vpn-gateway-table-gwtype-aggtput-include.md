|  | **VPN Gateway のスループット (1)** | **VPN Gateway の IPsec トンネルの最大数 (2)** | **ExpressRoute ゲートウェイのスループット** | **VPN Gateway と ExpressRoute の共存** |
| --- | --- | --- | --- | --- |
| **Basic SKU (3)** |100 Mbps |10 |500 Mbps |いいえ |
| **Standard SKU (4)** |100 Mbps |10 |1000 Mbps |はい |
| ** SKU (4)** |200 Mbps |30 |2000 Mbps |はい |

* (1) VPN のスループットは、同一 Azure リージョン内の VNet 間での測定値に基づく大まかな推定値です。 インターネット経由でのクロスプレミス接続では、このスループットが保証されるわけではありません。 この値は、達成可能な最大スループットです。
* (2) トンネルの数とは、RouteBased VPN を指しています。 PolicyBased VPN がサポートできるサイト間 VPN トンネルは 1 つのみです。
* (3) BGP は、Basic SKU ではサポートされていません。
* (4) PolicyBased VPN は、この SKU ではサポートされていません。 Basic SKU でのみサポートされています。

<!--HONumber=Oct16_HO2-->



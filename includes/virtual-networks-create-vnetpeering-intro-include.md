VNET ピアリングとは、同じリージョンに存在する 2 つの仮想ネットワークを Azure のバックボーン ネットワークを介して接続する機構です。ピアリングされた 2 つの仮想ネットワークは、あらゆる接続において、見かけ上 1 つの仮想ネットワークとして機能します。VNet ピアリングの基本的な概念については、[VNet ピアリングの概要](../articles/virtual-network/virtual-network-peering-overview.md)に関する記事を参照してください。

VNet ピアリングはパブリック プレビューの段階です。使用できるようにするには、以下のコマンドで登録する必要があります。

> [AZURE.NOTE] Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network –force
 

<!---HONumber=AcomDC_0803_2016-->
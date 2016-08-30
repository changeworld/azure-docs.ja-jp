<properties
   pageTitle="Azure VPN Gateway のリセット | Microsoft Azure"
   description="この記事では、Azure VPN Gateway をリセットする方法を紹介します。この記事は、従来のデプロイ モデルを使用して作成された VPN ゲートウェイを対象としています。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# PowerShell を使用して Azure VPN Gateway をリセットする


この記事では、PowerShell のコマンドレットを使用して、Azure VPN Gateway をリセットする方法を紹介します。次の手順は、従来のデプロイ モデルに適用されます。現時点では、Resource Manager デプロイメント モデルで作成された仮想ネットワーク ゲートウェイは、リセットできません。

サイト間 VPN トンネルのクロスプレミス VPN 接続が失われた場合、Azure VPN Gateway をリセットすることによって解決できる場合があります。この状況では、オンプレミスの VPN デバイスがすべて正しく機能していても、Azure VPN Gateway との間で IPsec トンネルを確立することができません。*Reset-AzureVNetGateway* コマンドレットを使用すると、ゲートウェイが再起動してクロスプレミス構成が再適用されます。ゲートウェイに割り当てられているパブリック IP アドレスはそのまま維持されます。つまり、VPN ルーターの構成を Azure VPN Gateway の新しいパブリック IP アドレスで更新する必要がありません。


ゲートウェイをリセットする前に、個々の IPsec サイト間 (S2S) VPN トンネルについて、以下に挙げた主な項目を確認してください。いずれか 1 つの項目でも不備があると、S2S VPN トンネルの接続が失われます。オンプレミスの VPN ゲートウェイと Azure VPN Gateway に使用されている構成を確認して修正すれば、そのゲートウェイ上で正常に機能している他の接続に対して無駄な再起動や中断を行わずに済みます。

ゲートウェイをリセットする前に以下の項目を確認してください。

- Azure とオンプレミスの両方の VPN ポリシーに、Azure VPN Gateway とオンプレミスの VPN ゲートウェイのインターネット IP アドレス (VIP) がどちらも正しく構成されていること。
- Azure の VPN ゲートウェイとオンプレミスの VPN ゲートウェイが同じ事前共有キーを持っていること。
- 暗号化、ハッシュ アルゴリズム、PFS (Perfect Forward Secrecy) など特定の IPsec/IKE 構成を適用する場合、Azure の VPN ゲートウェイとオンプレミスの VPN ゲートウェイとに、必ず同じ構成を適用すること。


## PowerShell を使用して VPN Gateway をリセットする

Azure VPN Gateway をリセットするための PowerShell コマンドレットは *Reset-AzureVNetGateway* です。各 Azure VPN Gateway は、アクティブ/スタンバイ構成で動作する 2 つの VM インスタンスから成ります。このコマンドを実行すると、現在アクティブな Azure VPN Gateway のインスタンスが直ちに再起動されます。再起動中のアクティブ インスタンスからスタンバイ インスタンスにフェールオーバーされる際に、わずかな時間差が生じます。通常、時間差は 1 分未満です。

次の例では、"ContosoVNet" という仮想ネットワークの Azure VPN Gateway をリセットしています。
 
		Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 	Error          :
	 	HttpStatusCode : OK
	 	Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 	Status         : Successful
		RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
		StatusCode     : OK


初回再起動で接続が復元されない場合は、同じコマンドを再度実行して、2 つ目の VM インスタンス (新しくアクティブになった方のゲートウェイ) を再起動してください。連続して 2 回の再起動が必要であった場合、アクティブとスタンバイの両方の VM インスタンスを再起動する分、1 回で済んだ場合よりもわずかに時間がかかります。この場合、VPN 接続の途切れが長引いて、両方の VM が再起動を完了するまでに最大 2 ～ 4 分かかります。

2 回再起動してもクロスプレミス接続の問題が解消しない場合は、Azure クラシック ポータルからサポート チケットを開いて Microsoft Azure サポートにお問い合わせください。

## 次のステップ
	
このコマンドレットの詳細については、[PowerShell リファレンス](https://msdn.microsoft.com/library/azure/mt270366.aspx)を参照してください。

<!---HONumber=AcomDC_0824_2016-->
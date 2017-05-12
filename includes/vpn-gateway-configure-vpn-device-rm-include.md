オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 すべての VPN デバイスの構成手順を紹介することはできませんが、以下のリンクの情報が参考になるかと思います。

- 適合する VPN デバイスについては、[VPN デバイス](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)に関するページを参照してください。 
- デバイスの構成設定へのリンクについては、「[検証済みの VPN デバイス](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)」を参照してください。 デバイスの構成に関するリンクは、入手できる範囲で記載しています。 最新の構成情報については必ず、デバイスの製造元にご確認ください。
- デバイス構成サンプルの編集については、[サンプルの編集](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing)に関するセクションを参照してください。
- IPsec/IKE のパラメーターについては、[パラメーター](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)に関するセクションを参照してください。
- VPN デバイスを構成する前に、使用する VPN デバイスに関して、[デバイスの互換性に関する既知の問題](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known)がないかどうかを確認してください。

VPN デバイスを構成する際に、次の情報が必要になります。

- 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
- 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。
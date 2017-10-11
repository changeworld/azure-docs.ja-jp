### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>ポイント対サイトのクライアント オペレーティング システムを使用すればできますか。

次のクライアント オペレーティング システムがサポートされています。

* Windows 7 (32 ビットおよび 64 ビット)
* Windows Server 2008 R2 (64 ビットのみ)
* Windows 8 (32 ビットおよび 64 ビット)
* Windows 8.1 (32 ビットおよび 64 ビット)
* Windows Server 2012 (64 ビットのみ)
* Windows Server 2012 R2 (64 ビットのみ)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>ポイント対サイト SSTP をサポートするためにソフトウェア VPN クライアントを使用できますか。

いいえ。 サポートは、上に示した Windows オペレーティング システムのバージョンのみに制限されています。

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>ポイント対サイト構成であることが VPN クライアント エンドポイントの数

マイクロソフトは、同時に仮想ネットワークに接続できるようにするには最大 128 の VPN クライアントをサポートします。

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>ポイント対サイト接続用に自分の内部 PKI ルート CA を使用できますか。

はい。 以前は、自己署名ルート証明書のみを使用できます。 ルート証明書を 20 個をアップロードすることもできます。

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>プロキシとポイント対サイト機能を使用してファイアウォールを経由できますか。

はい。 ファイアウォールを通過するトンネル SSTP (Secure Socket トンネリング プロトコル) を使用します。 このトンネルは、HTTPs 接続として表示されます。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>ポイント対サイト用に構成されたクライアント コンピューターを再起動すると、VPN に自動的に再接続されますか。

既定では、クライアント コンピューターはいない接続を再確立 VPN 自動的にします。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>ポイント対サイト サポート自動再接続と DDNS VPN クライアントにしますか?

自動再接続と DDNS は現在サポートされていませんポイント対サイト Vpn でします。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>サイト対サイトを持つことができ、ポイント対サイトの構成が同じ仮想ネットワークの共存しますか。

はい。 ゲートウェイはルートベースで VPN の種類を持っている場合、これら両方のソリューションは機能します。 クラシック デプロイ モデル動的ゲートウェイが必要です。 いないサポート ポイント対サイトの静的ルーティング VPN ゲートウェイまたはゲートウェイを使用して作業を行う、`-VpnType PolicyBased`コマンドレット。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>同時に複数の仮想ネットワークに接続するポイント対サイト クライアントを構成できますか。

はい、このことができます。 仮想ネットワークは重複する IP プレフィックスを持つことはできず、ポイント対サイトのアドレス空間は、仮想ネットワーク間で重複できません。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>サイト間またはポイント対サイト接続を介してどれくらいのスループットを想定できますか。

VPN トンネルの正確なスループットを維持するために困難です。 IPsec と SSTP は、暗号の量が多い VPN プロトコルです。 スループット、待機時間や、内部設置型とインターネットの間の帯域幅によって制限されます。
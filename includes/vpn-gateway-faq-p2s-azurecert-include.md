[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>ポイント対サイト接続で自社の内部 PKI ルート CA を使用できますか。

はい。 以前は、自己署名ルート証明書のみを使用できました。 現在も 20 ルート証明書までアップロードできます。

### <a name="what-tools-can-i-use-to-create-certificates"></a>証明書の作成にどのようなツールを使用できますか。

エンタープライズ PKI ソリューション (内部 PKI)、Azure PowerShell、MakeCert、OpenSSL を使用できます。

### <a name="certsettings"></a>証明書の設定およびパラメーターに関する指示はありますか。

* **内部 PKI/エンタープライズ PKI ソリューション:** [証明書を生成する](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)手順を参照してください。

* **Azure PowerShell:** 手順については、[Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) の記事を参照してください。

* **MakeCert:** 手順については、[MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。

* **OpenSSL:** 

    * 証明書をエクスポートするときは、ルート証明書を Base64 に変換してください。

    * クライアント証明書の場合:

      * 秘密キーを作成する際は、長さを 4096 として指定します。
      * 証明書を作成する際は、*-extensions* パラメーターに *usr_cert* を指定します。
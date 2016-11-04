<BR>

## FAQ
### 逆引き DNS レコードのコストはどのくらいですか?
無料です。 逆引き DNS レコードまたはクエリに追加コストはかかりません。

### 逆引き DNS レコードはインターネットから解決しますか?
はい。Cloud Services の逆引き DNS プロパティを設定すると、Azure は、すべてのインターネット ユーザーについて逆引き DNS レコードを解決するために必要なすべての DNS 委任と DNS ゾーンを管理します。

### Cloud Services の既定の逆引き DNS レコードは作成されますか?
いいえ。逆引き DNS はオプトイン機能です。既定の逆引き DNS レコードを構成しない設定の場合、レコードは作成されません。

### 完全修飾ドメイン名 (FQDN) の形式とは何ですか?
FQDN は順方向で指定します。末尾にはドットを指定する必要があります (例: “app1.contoso.com.”)。

### 指定した逆引き DNS の検証チェックが失敗すると、どうなりますか?
逆引き DNS の検証チェックが失敗すると、サービス管理操作は失敗します。必要に応じて逆引き DNS 値を修正し、再試行してください。

### Azure Websites では逆引き DNS を管理できますか?
Azure Websites では、逆引き DNS はサポートされていません。Azure PaaS ロールと IaaS 仮想マシンでは、逆引き DNS がサポートされます。

### Cloud Services 用に複数の逆引き DNS を構成できますか?
いいえ、Azure は Azure Cloud Services ごとに 1 つの逆引き DNS レコードをサポートしています。ただし、各 Azure Cloud Services は、独自の逆引き DNS レコードを持つことができます。

### Azure コンピューティング サービスから外部ドメインに電子メールを送信できますか?
いいえ。[こちら](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)に記載されているように、Azure コンピューティング サービスでは、外部ドメインへの電子メールの送信はサポートされていません。

<!---HONumber=AcomDC_0907_2016-->
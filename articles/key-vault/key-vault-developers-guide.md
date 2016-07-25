<properties
   pageTitle="Azure Key Vault 開発者ガイド | Microsoft Azure"
   description="開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/07/2016"
   ms.author="bruceper" />

# Azure Key Vault 開発者ガイド
Key Vault を使用すると、アプリケーション内から機密情報に安全にアクセスできるようになります。具体的には次のとおりです。

- 自分でコードを記述しなくても、キーとシークレットが保護され、アプリケーションから簡単に使用できます。
- 顧客に自分で専用のキーを所有して管理してもらうことができます。皆さんは主要なソフトウェア機能の提供に集中できます。また、このため、アプリケーションは顧客のテナント キーやシークレットに対して義務や潜在的責任を負うことはありません。
- アプリケーションで署名と暗号化のキーを使用しつつ、キー管理をアプリケーションの外部で実施できます。これにより、ソリューションは地理的に分散したアプリケーションを対象とすることができます。

Azure Key Vault の全般的な情報については、「[Azure Key Vault とは](key-vault-whatis.md)」を参照してください。

## ビデオ
このビデオでは、独自の Key Vault を作成して 「Hello Key Vault」 サンプル アプリケーションからそれを使用する方法について説明します。

ビデオで説明されているリソースへのリンク:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault のサンプル コード](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

[Key Vault Blog](http://aka.ms/kvblog) の指示に従って [Key Vault Forum](http://aka.ms/kvforum) に参加すると、詳細情報を得ることができます。



> [AZURE.VIDEO azure-key-vault-developer-quick-start]



## Key Vaults の作成と管理

コード内で Azure Key Vault を使用する前に、次の記事の説明に従って、REST、リソース マネージャー テンプレート、PowerShell、または CLI を使用してコンテナーを作成し、管理します。

- [REST を使用した Key Vault の作成と管理](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [PowerShell を使用した Key Vault の作成と管理](key-vault-get-started.md)
- [CLI を使用した Key Vault の管理](key-vault-manage-with-cli.md)
- [ARM テンプレートを使用した Key Vault の作成とシークレットの追加](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Key Vault に対する操作は、AAD によって認証され、コンテナーごとに定義された Key Vault 独自のアクセス ポリーによって許可されます。

## Key Vault を使用したコーディング

プログラマー向けの Key Vault 管理システムは、REST を基盤とするさまざまなインターフェイスで構成されています。 [Key Vault REST API リファレンス ](https://msdn.microsoft.com/library/azure/dn903609.aspx)

承認された場合は、以下を実行できます。

- 暗号化キーを、[作成](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[インポート](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[削除](https://msdn.microsoft.com/library/azure/dn903611.aspx)などの操作を使用して管理する

- シークレットを、[取得](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn986818.aspx)、[削除](https://msdn.microsoft.com/library/azure/dn903613.aspx)などの操作を使用して管理する

- 暗号化キーを、[署名](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[確認](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx)、[暗号化](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[暗号化解除](https://msdn.microsoft.com/library/azure/dn878097.aspx)操作で使用する

Key Vault の操作には次の SDK を使用できます。

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt430941.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET SDK ドキュメント](https://msdn.microsoft.com/library/mt430941.aspx)|[Node.js SDK ドキュメント](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK パッケージ](https://azure.microsoft.com/documentation/api/)|[Node.js SDK パッケージ](https://www.npmjs.com/package/azure-keyvault)|


アプリケーションで Key Vault を使用する詳細な例については、以下のものを参照してください。

- .NET サンプル アプリケーション *HelloKeyVault* と Azure Web サービスの例：[Azure Key Vault のコード サンプル](http://www.microsoft.com/download/details.aspx?id=45343)
- Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できるチュートリアル: [Web アプリケーションからの Azure Key Vault の使用](key-vault-use-from-web-application.md)

## 方法

次の記事とシナリオは、Azure Key Vault を操作するための、タスクの具体的なガイダンスを提供します。

- [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md) - この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。
- [デプロイ時にセキュリティで保護された値 (パスワードなど) を渡す方法](../resource-manager-keyvault-parameter.md) - デプロイメント時にパラメーターとしてセキュリティで保護された値 (パスワードなど) を渡す必要がある場合、Azure Key Vault にシークレットとしてその値を格納し、他のリソース マネージャー テンプレートで値を参照することができます。
- [Key Vault を使用して SQL Server で拡張キーを管理する方法](https://msdn.microsoft.com/library/dn198405.aspx) - Azure Key Vault 用の SQL Server Connector により、SQL Server と VM 内の SQL で拡張キー管理 (EKM) プロバイダーとして Azure Key Vault サービスを利用して、Transparent Data Encryption (透過的なデータ暗号化、TDE)、バックアップ暗号化、Column Level Encryption (列レベル暗号化、CLE)など、アプリケーション リンクの暗号化キーを保護できます。
- [Key Vault から VM に証明書をデプロイする方法](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Azure の VM で実行するクラウド アプリケーションには証明書が必要です。今すぐこの VM で証明書を取得する方法を説明します。
- [Windows および Linux IaaS VM の Azure Disk Encryption と Key Vault を統合する方法](../azure-security-disk-encryption.md) - このディスク暗号化ソリューションは Azure Key Vault と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、Azure ストレージで保存中の仮想マシン ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。
- [エンド ツー エンドのキー ローテーションと監査で Key Vault を設定する方法](key-vault-key-rotation-log-monitoring.md) - Azure Key Vault を使用して、キー ローテーションと監査を設定する方法について説明します。

Key Vault と Azure の統合と使用に関するその他のタスク固有のガイダンスについては、[Ryan Jones による Key Vault 向け ARM テンプレート サンプル](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)をご覧ください。

## Key Vault との統合

Key Vault を利用したり Key Vault と連携したりするその他のシナリオとサービスについては、以下の記事で取り上げています。

- [Azure Disk Encryption](../azure-security-disk-encryption.md) では、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、OS およびデータ ディスクのボリュームの暗号化を提供します。ソリューションは Azure Key Vault と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、Azure ストレージで保存中の仮想マシン ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。


## 対応ライブラリ

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0): 識別子からキーを検索し、キーを使用して操作を実行する、`IKey` および `IKeyResolver` インターフェイスを提供します。

- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0): Azure Key Vault の拡張機能を提供します。

## その他の Key Vault リソース
- [Key Vault Blog](http://aka.ms/kvblog)
- [Key Vault Forum](http://aka.ms/kvforum)

<!---HONumber=AcomDC_0713_2016-->
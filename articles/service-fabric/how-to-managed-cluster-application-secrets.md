---
title: Microsoft Azure Service Fabric マネージド クラスターにアプリケーション シークレットをデプロイする
description: Microsoft Azure Service Fabric アプリケーション シークレットと、それらをマネージド クラスターにデプロイする方法について説明します
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5b31e06b88d38ab0d5500e69b6733e739e2b3dae
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544737"
---
# <a name="deploy-application-secrets-to-a-service-fabric-managed-cluster"></a>Microsoft Azure Service Fabric マネージド クラスターにアプリケーション シークレットをデプロイする

シークレットは、ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など、機密情報である可能性があります。 Azure Key Vault を使用して、Microsoft Azure Service Fabric マネージド クラスターのキーとシークレットを管理し、この記事に利用することをお勧めします。 ただし、アプリケーションでのシークレットの " *使用* " はクラウド プラットフォームに依存しないので、クラスターでホストされている任意の場所にアプリケーションをデプロイできます。

サービスの構成設定を管理する方法として、[サービス構成パッケージ][config-package]を使用することが推奨されます。 構成パッケージはバージョン管理されており、正常性の検証と自動ロールバックを含む管理されたローリング アップグレードによって更新可能です。 グローバル サービスの停止の可能性が低減されるため、グローバル構成をお勧めします。 暗号化されたシークレットも例外ではありません。 Service Fabric には、証明書の暗号化を使用して、構成パッケージの Settings.xml ファイル内の値を暗号化および暗号化解除する機能が組み込まれています。

次の図は、Service Fabric アプリケーションでのシークレットの管理の基本的なフローを示しています。

![シークレットの管理の概要][overview]

このフローには、次の 4 つの主な手順があります。

1. データ暗号化証明書を取得します。
2. クラスターに証明書をインストールします。
3. アプリケーションをデプロイするときに、証明書を使用してシークレット値を暗号化し、サービスの Settings.xml 構成ファイルに挿入します。
4. 同じ暗号化証明書によって暗号化を解除して、Settings.xml から暗号化された値を読み取ります。 

ここでは、証明書の安全な格納場所として [Azure Key Vault][key-vault-get-started] を使用します。また、Azure の Service Fabric マネージド クラスターにインストールされている証明書を取得する方法としても使用します。

アプリケーション シークレットを実装する方法の例については、[アプリケーション シークレットの管理](service-fabric-application-secret-management.md)に関する記事を参照してください。

それ以外では、[KeyVaultReference](service-fabric-keyvault-references.md) もサポートしています。 Service Fabric の KeyVaultReference サポートにより、Key Vault に格納されているシークレットの URL を参照するだけで、簡単にシークレットをアプリケーションにデプロイできます

## <a name="create-a-data-encipherment-certificate"></a>データ暗号化証明書を作成する
独自のキー コンテナーを作成して証明書を設定するには、Azure Key Vault から [Azure CLI、PowerShell、ポータルなど][key-vault-certs]を使用する手順に従います。

>[!NOTE]
> コンピューティング リソース プロバイダーが Key Vault から証明書を取得し、クラスター ノードにインストールするには、その Key Vault を[テンプレート デプロイ用に有効にする](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI)必要があります。

## <a name="install-the-certificate-in-your-cluster"></a>クラスターへの証明書のインストール
この証明書は、クラスター内の各ノードにインストールする必要があります。Service Fabric マネージド クラスターを使用すると、これを簡単に行うことができます。 マネージド クラスター サービスからノードにバージョン固有のシークレットがプッシュされると、プライベート ルート CA をノードにインストールする場合など、頻繁に変更されないシークレットをインストールすることができます。 ほとんどの実稼働ワークロードでは、[KeyVault 拡張機能][key-vault-windows]を使用することをお勧めしています。 Key Vault VM 拡張機能には、静的なバージョンと比較して Azure Key Vault に保存されている証明書を自動更新する機能が備わっています。

マネージド クラスターの場合、3 つの値が必要になります。2 つは Azure Key Vault からのもので、1 つはノードのローカル ストア名として決定します。

パラメーター: 
* `Source Vault`: これは 
    * 例: /subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1
* `Certificate URL`: これは完全なオブジェクト識別子であり、大文字と小文字は区別されず、変更できません
    * https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}
* `Certificate Store`: 証明書が配置されるノード上のローカル証明書ストアです
    * ノード上の証明書ストア名。例: "MY"

Service Fabric マネージド クラスターでは、ノードにバージョン固有のシークレットを追加するための 2 つの方法がサポートされています。

1. 最初のクラスター作成時にポータルを使用する方法。上記の値をこの領域に挿入します。

![ポータル シークレットの入力][sfmc-secrets]

2. Azure Resource Manager を使用した作成時、またはいつでも

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "properties": {
    "vmSecrets": [
      {
        "sourceVault": {
          "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
          {
            "certificateStore": "MY",
            "certificateUrl": "https://mykeyvault1.vault.azure.net/certificates/{certificatename}/{secret-version}"
          }
        ]
      }
    ]
  }
}
```

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[key-vault-certs]: ../key-vault/certificates/quick-create-cli.md
[config-package]: service-fabric-application-and-service-manifests.md
[key-vault-windows]: ../virtual-machines/extensions/key-vault-windows.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
[sfmc-secrets]:./media/how-to-managed-cluster-application-secrets/sfmc-secrets.png

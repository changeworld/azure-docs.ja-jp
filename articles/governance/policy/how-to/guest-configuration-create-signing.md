---
title: ゲスト構成パッケージへの署名方法
description: 必要に応じて、ゲスト構成コンテンツ パッケージに署名し、署名されたコンテンツのみを許可するエージェントを強制できます
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 7c7d120f17e32b3ea9accb6697ac66f4afcbb879
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868576"
---
# <a name="how-to-sign-guest-configuration-packages"></a>ゲスト構成パッケージへの署名方法

ゲスト構成カスタム ポリシーでは、SHA256 ハッシュを使用して、ポリシー パッケージが変更されていないことが検証されます。 必要に応じて、お客様は証明書を使ってパッケージに署名し、署名されたコンテンツのみを許可するようにゲスト構成拡張機能を強制することもできます。

このシナリオを有効にするには、2 つのステップを実行する必要があります。 コマンドレットを実行してコンテンツ パッケージに署名し、コードが署名されていることを要求する必要があるようにマシンにタグを追加します。

## <a name="signature-validation-using-a-code-signing-certificate"></a>コード署名証明書を使用した署名の検証

署名検証機能を使用するには、`Protect-GuestConfigurationPackage` コマンドレットを実行して、発行前にパッケージに署名します。 このコマンドレットには "コード署名" 証明書が必要です。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` コマンドレットのパラメーター:

- **パス**: ゲスト構成パッケージの完全なパス。
- **Certificate**: パッケージに署名するためのコード署名証明書。 このパラメーターは、Windows 用のコンテンツに署名する場合にのみサポートされます。

## <a name="certificate-requirements"></a>証明書の要件

GuestConfiguration エージェントにより、Windows マシンの場合は "信頼されたルート証明機関" に、Linux マシンの場合はパス `/usr/local/share/ca-certificates/extra` に、証明書の公開キーが存在していることが求められます。 署名されたコンテンツをノードで検証するには、カスタム ポリシーを適用する前に、マシンに証明書の公開キーをインストールします。 このプロセスは、VM 内で任意の方法を使うか、Azure Policy を使って、行うことができます。 [証明書を使用してマシンをデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows)には、テンプレートの例を使用できます。
Key Vault のアクセス ポリシーでは、デプロイ中にコンピューティング リソース プロバイダーが証明書にアクセスできるようにする必要があります。 詳しい手順については、[Azure Resource Manager の仮想マシンの Key Vault を設定する](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)に関する記事をご覧ください。

署名証明書から公開キーをエクスポートしてマシンにインポートする例を次に示します。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## <a name="tag-requirements"></a>タグの要件

コンテンツを発行した後、コード署名が必要なすべての仮想マシンに、名前が `GuestConfigPolicyCertificateValidation` で値が `enabled` のタグを追加します。 Azure Policy を使用して大規模にタグを配信する方法については、[タグのサンプル](../samples/built-in-policies.md#tags)に関する記事を参照してください。 このタグを配置すると、`New-GuestConfigurationPolicy` コマンドレットを使って生成されるポリシー定義では、ゲスト構成拡張による要件が有効になります。

## <a name="next-steps"></a>次の手順

- 開発環境から[パッケージ成果物をテストする](./guest-configuration-create-test.md)。
- 自分のマシンからアクセスできるように、[パッケージ成果物を公開する](./guest-configuration-create-publish.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](./guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成のポリシー割り当てのコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を確認する方法を学ぶ。

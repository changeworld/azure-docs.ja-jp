---
title: Azure Key Vault 証明書を使用した TLS 終端
description: HTTPS 対応リスナーにアタッチされているサーバー証明書の Key Vault と Azure Application Gateway を統合する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: c44691496df3610688f13843f3c5ccbfe20f625a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004067"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault 証明書を使用した TLS 終端

[Azure Key Vault](../key-vault/general/overview.md) はプラットフォームマネージド シークレット ストアです。シークレット、キー、TLS または SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway の v2 SKU に制限されます。

Application Gateway には、TLS 終端の 2 つのモデルが用意されています。

- リスナーにアタッチされている TLS または SSL 証明書を指定します。 このモデルは、TLS 終端のために TLS または SSL 証明書を Application Gateway に渡す従来の方法を示しています。
- HTTPS 対応リスナーの作成時に、既存の Key Vault 証明書またはシークレットへの参照を指定します。

Application Gateway と Key Vault の統合には、次のようにさまざまな利点があります。

- セキュリティの強化 (TLS または SSL 証明書がアプリケーション開発チームによって直接処理されないため)。 統合により、別のセキュリティ チームが以下のこと実行できます。
  * アプリケーション ゲートウェイを設定する。
  * アプリケーション ゲートウェイのライフ サイクルを制御する。
  * ご使用のキー コンテナーに格納されている証明書にアクセスする権限を特定のアプリケーション ゲートウェイに付与する。
- ご使用のキー コンテナーに既存の証明書をインポートできます。 また、任意の信頼できる Key Vault パートナーと共に Key Vault API を使用して、新しい証明書を作成したり管理したりできます。
- ご使用のキー コンテナーに格納されている証明書を自動更新できます。

## <a name="supported-certificates"></a>サポートされている証明書

Application Gateway で現在サポートされているのは、ソフトウェアで検証された証明書のみです。 ハードウェア セキュリティ モジュール (HSM) で検証された証明書はサポートされません。 

Key Vault 証明書を使用するように Application Gateway を構成すると、そのインスタンスによって Key Vault から証明書が取得され、TLS 終端のためにその証明書がローカルにインストールされます。 インスタンスによって 4 時間ごとに Key Vault がポーリングされ、証明書の更新バージョン (存在する場合) が取得されます。 更新された証明書が検出されると、HTTPS リスナーに現在関連付けられている TLS または SSL 証明書が自動的にローテーションされます。 

証明書を参照するために、Key Vault 内ではシークレット識別子が Application Gateway によって使用されます。 Azure PowerShell、Azure CLI、または Azure Resource Manager の場合、バージョンを指定しないシークレット識別子を使用することを強く推奨します。 これにより、ご自身のキー コンテナー内で新しいバージョンが利用可能にると、Application Gateway によって証明書が自動的にローテーションされます。 バージョンのないシークレット URI の例は `https://myvault.vault.azure.net/secrets/mysecret/` です。

Azure portal では、シークレットではなく Key Vault 証明書のみがサポートされます。 Application Gateway は引き続き Key Vault からのシークレットの参照をサポートしますが、PowerShell、Azure CLI、API、Azure Resource Manager テンプレート (ARM テンプレート) など、ポータル リソース以外を使用している場合のみに限られます。

> [!WARNING]
> 現在、Azure Application Gateway でサポートされているのは、Application Gateway リソースと同じサブスクリプション内にある Key Vault アカウントのみです。 Application Gateway とは異なるサブスクリプションの Key Vault を選択すると、エラーが発生します。

## <a name="certificate-settings-in-key-vault"></a>Key Vault 内の証明書の設定

TLS 終端の場合、Application Gateway は、Personal Information Exchange (PFX) 形式の証明書のみサポートしています。 既存の証明書をインポートするか、ご自身のキー コンテナーで新しい証明書を作成することがします。 エラーを回避するには、Key Vault 内で証明書の状態が **[有効]** に設定されていることを確認します。

## <a name="how-integration-works"></a>統合のしくみ

Application Gateway と Key Vault の統合は、3 段階の構成プロセスで行われます。

![Application Gateway と Key Vault を統合するための 3 つのステップを示す図。](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てマネージド ID を作成するか、既存の ID を再利用します。 Application Gateway は、マネージド ID を使用して、ユーザーに代わって Key Vault から証明書を取得します。 詳しくは、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」をご覧ください。 

この手順では、Azure Active Directory テナントに新しい ID を作成します。 この ID は、ID を作成するために使用されたサブスクリプションによって信頼されます。

### <a name="configure-your-key-vault"></a>キー コンテナーの構成

ご自身のキー コンテナーでユーザー割り当てマネージド ID を使用するためのアクセス ポリシーを定義します。
    
1. Azure portal で、**Key Vault** に移動します。
1. **[アクセス ポリシー]** ウィンドウを開きます。
1. **コンテナー アクセス ポリシー** 権限モデルを使用している場合: **[+ アクセス ポリシーの追加]** を選択し、 **[シークレットのアクセス許可]** で **[取得]** を選択して、 **[プリンシパルの選択]** で、ご自身のユーザー割り当てマネージド ID を選択します。 次に、 **[保存]** を選択します。
   
   **Azure ロールベースのアクセス制御** 権限モデルを使用している場合: ユーザー割り当てマネージド ID のロールの割り当てを、**Key Vault シークレット ユーザー** ロールの Azure キー コンテナーに追加します。

2021 年 3 月 15 日現在、Key Vault は、Azure Key Vault に対する認証にユーザー マネージド ID を利用することで、Application Gateway を、信頼できるサービスとして認識します。  サービス エンドポイントを使用し、信頼できるサービス オプションを、キー コンテナーのファイアウォールに対して有効にすると、セキュリティで保護されたネットワーク境界をAzure 内で構築できます。 Key Vault に対するすべてのネットワーク (インターネット トラフィックを含む) からのトラフィックへのアクセスを拒否できますが、ご自身のサブスクリプションで、Application Gateway リソースに対して Key Vault をアクセス可能にすることはできます。

制限付きキー コンテナーを使用している場合は、次の手順に従って、ファイアウォールと仮想ネットワークが使用されるように Application Gateway を構成します。 

1. Azure portal のお使いの キー コンテナーで、 **[ネットワーク]** を選択します。
1. **[ファイアウォールと仮想ネットワーク]** タブで、 **[プライベート エンドポイントと選択されたネットワーク]** を選択します。
1. **[仮想ネットワーク]** で、 **[+ 既存の仮想ネットワークの追加]** を選択し、Application Gateway インスタンスの仮想ネットワークとサブネットを追加します。 このプロセス中に、チェックボックスをオンにして、`Microsoft.KeyVault` サービス エンドポイントも構成します。
1. **[はい]** を選択し、信頼できるサービスが、キー コンテナーのファイアウォールをバイパスできるようにします。
  
![ファイアウォールと仮想ネットワークを使用して Application Gateway を構成するための選択を示すスクリーンショット。](media/key-vault-certs/key-vault-firewall.png)

Azure CLI または PowerShell のいずれかを使用して ARM テンプレート経由で、あるいは Azure portal からデプロイされた Azure アプリケーション経由で、Application Gateway インスタンスをデプロイする場合、SSL 証明書は Base64 でエンコードされた PFX ファイルとしてキー コンテナー内に格納されます。 「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)」の手順を実行する必要があります。 

特に、`enabledForTemplateDeployment` を `true` に設定することが重要です。 証明書は、パスワードがある場合とない場合があります。 パスワードが設定された証明書の場合、次の例は、Application Gateway の ARM テンプレート構成に対する `properties` 内の `sslCertificates` エントリの構成を示しています。 

```
"sslCertificates": [
    {
        "name": "appGwSslCertificate",
        "properties": {
            "data": "[parameters('appGatewaySSLCertificateData')]",
            "password": "[parameters('appGatewaySSLCertificatePassword')]"
        }
    }
]
```

`appGatewaySSLCertificateData` と `appGatewaySSLCertificatePassword` の値は、「[動的 ID でのシークレットの参照](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)」で説明されているように、キー コンテナーから検索されます。 参照がどのように行われるかを確認するには、`parameters('secretName')` から後方に参照をたどります。 証明書がパスワードレスである場合は、`password` エントリを省略します。

### <a name="configure-application-gateway"></a>Application Gateway の構成

ユーザー割り当てマネージド ID を作成し、ご自身のキー コンテナーを構成したら、ID およびアクセス管理 (IAM) を使用して、お使いの Application Gateway インスタンスに対してマネージド ID を割り当てることができます。 PowerShell については、「[Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity)」を参照してください。

## <a name="investigating-and-resolving-key-vault-errors"></a>Key Vault エラーの調査と解決

Azure Application Gateway は、Key Vault 上で更新された証明書バージョンを 4 時間ごとにポーリングするだけではありません。 すべてのエラーをログに記録し、Azure Advisor と統合されているため、すべての構成の誤りが推奨事項として表示されます。 推奨事項には、問題と、関連付けられている Key Vault リソースの詳細が含まれています。 この情報と[トラブルシューティング ガイド](../application-gateway/application-gateway-key-vault-common-errors.md)を使用することで、こうした構成エラーをすばやく解決できます。 

問題が検出されたときに最新の情報が得られるように [Advisor のアラートを構成](../advisor/advisor-alerts-portal.md)することを強くお勧めします。 この特定のケースに対してアラートを設定するには、推奨事項の種類として **[Resolve Azure Key Vault issue for your Application Gateway]\(Application Gateway の Azure Key Vault に関する問題を解決する\)** を使用します。 

## <a name="next-steps"></a>次のステップ

[Azure PowerShell で Key Vault 証明書を使用して TLS 終端を構成する](configure-keyvault-ps.md)

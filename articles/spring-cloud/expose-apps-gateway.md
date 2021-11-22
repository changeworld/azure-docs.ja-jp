---
title: App Gateway を使用してアプリケーションをインターネットに公開する
titleSuffix: Azure Spring Cloud
description: App Gateway を使用してアプリケーションをインターネットに公開する方法
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: e23503c7e38009a8ac6b5da82dd17e26c7aeeaee
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404300"
---
# <a name="expose-applications-to-the-internet-using-application-gateway"></a>App Gateway を使用してアプリケーションをインターネットに公開する

この記事では、App Gateway を使用してアプリケーションをインターネットに公開する方法について説明します。 Azure Spring Cloud サービス インスタンスが仮想ネットワークにデプロイされている場合、このサービス インスタンス上のアプリケーションには、プライベート ネットワークからのみアクセスできます。 インターネットでアプリケーションにアクセスできるようにするには、Azure Application Gateway と統合する必要があります。

## <a name="prerequisites"></a>前提条件

- [Azure CLI バージョン 2.0.4 以降](/cli/azure/install-azure-cli)。
- 仮想ネットワークにデプロイされた Azure Spring Cloud サービス インスタンスと、既定の`.private.azuremicroservices.io` ドメイン サフィックスを使用してプライベート ネットワーク経由でアクセス可能なアプリケーション。 詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](./how-to-deploy-in-azure-virtual-network.md)」を参照してください
- アプリケーションにアクセスするために使用されるカスタム ドメイン。
- Key Vault に格納されている証明書。これは、HTTPS リスナーの確立に使用されるカスタム ドメインと一致します。 詳細については、「[チュートリアル: Azure Key Vault に証明書をインポートする](../key-vault/certificates/tutorial-import-certificate.md)」を参照してください。

## <a name="configure-application-gateway-for-azure-spring-cloud"></a>Azure Spring Cloud 向けに App Gateway を構成する

ブラウザーに表示されるドメイン名は、Application Gateway でトラフィックを Azure Spring Cloud バックエンドに転送するために使用されるホスト名と同じにすることをお勧めします。 この推奨事項に従うと、Application Gateway を使用して、Azure Spring Cloud でホストされ、仮想ネットワークに存在するアプリケーションを公開するときに最高のエクスペリエンスが提供されます。 Application Gateway によって公開されるドメインが、Azure Spring Cloud で受け入れられるドメインと異なる場合、Cookie と生成されたリダイレクト URL (例) が壊れる可能性があります。

Azure Spring Cloud の前面に Application Gateway を構成するには、次の手順に従います。

1. 「[仮想ネットワークに Azure Spring Cloud をデプロイする](./how-to-deploy-in-azure-virtual-network.md)」の手順に従います。
1. 選択したドメインの証明書を取得し、Key Vault に格納します。 詳細については、「[チュートリアル: Azure Key Vault に証明書をインポートする](../key-vault/certificates/tutorial-import-certificate.md)」を参照してください。
1. Azure Spring Cloud にデプロイされたアプリで、カスタム ドメインとそれに対応する Key Vault 内の証明書を構成します。 詳細については、「[チュートリアル: 既存のカスタム ドメインを Azure Spring Cloud にマップする](./tutorial-custom-domain.md)」を参照してください。
1. 次の一覧に従って構成された仮想ネットワークに Application Gateway をデプロイします。
   - 末尾に `private.azuremicroservices.io` が付いたドメインによって参照されるバックエンド プールで Azure Spring Cloud を使用します。
   - Key Vault 内の証明書と同じ証明書を使用して HTTPS リスナーを含めます。
   - 末尾に `private.azuremicroservices.io` が付いたドメインではなく、Azure Spring Cloud で構成されたカスタム ドメイン名を使用する HTTP 設定を使用して仮想ネットワークを構成します。
1. Application Gateway を指すようにパブリック DNS を構成します。

## <a name="define-variables"></a>変数の定義

次に、以下のコマンドを使用して、「[Azure 仮想ネットワークに Azure Spring Cloud をデプロイする](./how-to-deploy-in-azure-virtual-network.md)」の指示に従って作成したリソース グループと仮想ネットワークに対して変数を定義します。 実際の環境に基づいて値をカスタマイズします。 `SPRING_APP_PRIVATE_FQDN` を定義する際には、URI から `https://` を削除します。

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_CLOUD_NAME='name-of-spring-cloud-instance'
APPNAME='name-of-app-in-azure-spring-cloud'
SPRING_APP_PRIVATE_FQDN='$APPNAME.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

次のコマンドを使用して、Azure CLI にサインインし、アクティブなサブスクリプションを選択します。

```azurecli
az login
az account set --subscription $SUBSCRIPTION
```

## <a name="acquire-a-certificate"></a>証明書を取得する

### <a name="use-a-publicly-signed-certificate"></a>[公的に署名された証明書を使用する](#tab/public-cert)

運用環境にデプロイする場合、公的に署名された証明書を使用する可能性が高くなります。 この場合、証明書を Azure Key Vault にインポートします。 詳細については、「[チュートリアル: Azure Key Vault に証明書をインポートする](../key-vault/certificates/tutorial-import-certificate.md)」を参照してください。 証明書に証明書チェーン全体が含まれていることを確認してください。

### <a name="use-a-self-signed-certificate"></a>[自己署名証明書の使用](#tab/self-signed-cert)

テストまたは開発のために自己署名証明書が必要な場合、それを作成する必要があります。 また、証明書の "サブジェクトの別名" の一覧に、アプリケーションを公開するドメイン名を確実に含める必要もあります。 Azure Key Vault を介して自己署名証明書を作成する場合、Azure portal を使用して作成します。 また、Azure CLI を使用する場合、ポリシー JSON ファイルが必要です。

既定のポリシーを要求するには、次のコマンドを使用します。

```azurecli
az keyvault certificate get-default-policy
```

次に、次の例に示すように、ポリシー JSON を調整し、`subject` と `SubjectAlternativeNames` を示します。

```json
{
  // ...
    "subject": "C=US, ST=WA, L=Redmond, O=Contoso, OU=Contoso HR, CN=myapp.mydomain.com",
    "subjectAlternativeNames": {
        "dnsNames": [
            "myapp.mydomain.com",
            "*.myapp.mydomain.com"
        ],
        "emails": [
            "hello@contoso.com"
        ],
          "upns": []
      }
  // ...
}
```

ポリシー JSON の更新が完了したら ([証明書ポリシーを更新する](/rest/api/keyvault/update-certificate-policy/update-certificate-policy)」を参照)、次のコマンドを使用して、Key Vault で自己署名証明書を作成できます。

```azurecli
KV_NAME='name-of-key-vault'
CERT_NAME_IN_KV='name-of-certificate-in-key-vault'

az keyvault certificate create \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --policy "$KV_CERT_POLICY"
```

---

## <a name="configure-the-public-domain-name-on-azure-spring-cloud"></a>Azure Spring Cloud でパブリック ドメイン名を構成する

トラフィックは、パブリック ドメイン名を使用して Azure Spring Cloud にデプロイされたアプリケーションに送信されます。 このホスト名をリッスンし、HTTPS 経由でリッスンするようにアプリケーションを構成するには、次のコマンドを使用して、カスタム ドメインをアプリに追加します。

```azurecli
KV_NAME='name-of-key-vault'
KV_RG='resource-group-name-of-key-vault'
CERT_NAME_IN_ASC='name-of-certificate-in-Azure-Spring-Cloud'
CERT_NAME_IN_KV='name-of-certificate-with-intermediaries-in-key-vault'
DOMAIN_NAME=myapp.mydomain.com

# provide permissions to ASC to read the certificate from Key Vault:
VAULTURI=$(az keyvault show -n $KV_NAME -g $KV_RG --query properties.vaultUri -o tsv)

# get the object id for the Azure Spring Cloud Domain-Management Service Principal:
ASCDM_OID=$(az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId --output tsv)

# allow this Service Principal to read and list certificates and secrets from Key Vault:
az keyvault set-policy -g $KV_RG -n $KV_NAME  --object-id $ASCDM_OID --certificate-permissions get list --secret-permissions get list

# add custom domain name and configure TLS using the certificate:
az spring-cloud certificate add \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --name $CERT_NAME_IN_ASC \
    --vault-certificate-name $CERT_NAME_IN_KV \
    --vault-uri $VAULTURI
az spring-cloud app custom-domain bind \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --domain-name $DOMAIN_NAME \
    --certificate $CERT_NAME_IN_ASC \
    --app $APPNAME
```

> [!NOTE]
> Application Gateway で TLS 終端を実行する場合、開発とテストのために、Azure Spring Cloud で証明書を構成したり、Azure Spring Cloud で Key Vault から読み取るためのアクセス許可を提供したりする必要はありません。 必要なのは、ドメイン名をバインドすることだけです。これは、次のコマンドで実行できます。
>
> ```azurecli
> az spring-cloud app custom-domain bind \
>     --resource-group $RESOURCE_GROUP \
>     --service $SPRING_CLOUD_NAME \
>     --domain-name $DOMAIN_NAME \
>     --app $APPNAME
> ```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

作成する Azure Application Gateway は、Azure Spring Cloud サービス インスタンスと同じ仮想ネットワーク、または Azure Spring Cloud サービス インスタンスとピアリングされた仮想ネットワークに参加します。 最初に、`az network vnet subnet create` を使用して、仮想ネットワーク内にアプリケーション ゲートウェイ用の新しいサブネットを作成し、さらに `az network public-ip create` を使用して、アプリケーション ゲートウェイのフロントエンドとしてパブリック IP アドレスを作成します。

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name $APPLICATION_GATEWAY_SUBNET_NAME \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --address-prefix $APPLICATION_GATEWAY_SUBNET_CIDR
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-a-managed-identity-for-application-gateway"></a>Application Gateway のマネージド ID を作成する

Application Gateway は、証明書を読み取るために Key Vault にアクセスできる必要があります。 このためには、ユーザー割り当て[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用します。 次のコマンドを使用して、マネージド ID を作成します。

```azurecli
APPGW_IDENTITY_NAME='name-for-appgw-managed-identity'
az identity create \
    --resource-group $RESOURCE_GROUP \
    --name $APPGW_IDENTITY_NAME
```

次に、マネージド ID の objectId をフェッチします。これは、後で Key Vault で証明書にアクセスする権限を付与するために使用されます。

```azurecli
APPGW_IDENTITY_CLIENTID=$(az identity show --resource-group $RESOURCE_GROUP --name $APPGW_IDENTITY_NAME --query clientId --output tsv)
APPGW_IDENTITY_OID=$(az ad sp show --id $APPGW_IDENTITY_CLIENTID --query objectId --output tsv)
```

## <a name="set-policy-on-key-vault"></a>Key Vault にポリシーを設定する

次のコマンドを使用して Key Vault を構成し、Application Gateway のマネージド ID で、Key Vault に格納されている証明書にアクセスできるようにします。

```azurecli
az keyvault set-policy \
    --name $KV_NAME \
    --resource-group $KV_RG \
    --object-id $APPGW_IDENTITY_OID \
    --secret-permissions get list \
    --certificate-permissions get list
```

## <a name="create-application-gateway"></a>Application Gateway の作成

`az network application-gateway create` を使用してアプリケーション ゲートウェイを作成し、バックエンド プール内のサーバーとしてアプリケーションのプライベート完全修飾ドメイン名 (FQDN) を指定します。 必ず、ユーザー割り当てマネージド ID を使用し、証明書のシークレット ID を使用して Key Vault 内の証明書を指すようにします。 次に、`az network application-gateway http-settings update` を使用して、パブリック ホスト名を使用するように HTTP 設定を更新します。

```azurecli
APPGW_NAME='name-for-application-gateway'

KEYVAULT_SECRET_ID_FOR_CERT=$(az keyvault certificate show --name $CERT_NAME_IN_KV --vault-name $KV_NAME --query sid --output tsv)

az network application-gateway create \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --capacity 2 \
    --sku Standard_v2 \
    --frontend-port 443 \
    --http-settings-cookie-based-affinity Disabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --subnet $APPLICATION_GATEWAY_SUBNET_NAME \
    --servers $SPRING_APP_PRIVATE_FQDN \
    --key-vault-secret-id $KEYVAULT_SECRET_ID_FOR_CERT \
    --identity $APPGW_IDENTITY_NAME
```

Azure によってアプリケーション ゲートウェイが作成されるのに最大 30 分かかる場合があります。

>[!NOTE]
> Application Gateway で TLS 終端を実行する必要がある場合、開発とテストを行うには、`http-settings-port` を `80` に、`http-settings-protocol` を `Https` に変更し、引き続き次の "公的に署名された証明書を使用する" の指示に従います。

### <a name="update-http-settings-to-use-the-domain-name-towards-the-backend"></a>バックエンドに対してドメイン名を使用するように HTTP 設定を更新する

#### <a name="use-a-publicly-signed-certificate"></a>[公的に署名された証明書を使用する](#tab/public-cert-2)

末尾に ".private.azuremicroservices.io" が付いたドメインの代わりにパブリック ドメイン名をホスト名として使用するように HTTP 設定を更新して、トラフィックを Azure Spring Cloud に送信します。

```azurecli
az network application-gateway http-settings update \
    --resource-group $RESOURCE_GROUP \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings
```

#### <a name="use-a-self-signed-certificate"></a>[自己署名証明書の使用](#tab/self-signed-cert-2)

末尾に ".private.azuremicroservices.io" が付いたドメインの代わりにパブリック ドメイン名をホスト名として使用するように HTTP 設定を更新して、トラフィックを Azure Spring Cloud に送信します。 自己署名証明書を使用する場合、それは、Application Gateway の HTTP 設定で許可リストに登録されている必要があります。

証明書を許可リストに登録するには、最初に次のコマンドを使用して、証明書のパブリック部分を Key Vault からフェッチします。

```azurecli
az keyvault certificate download \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --file ./selfsignedcert.crt \
    --encoding DER
```

次に、次のコマンドを使用して、それを Application Gateway にアップロードします。

```azurecli
az network application-gateway root-cert create \
    --resource-group $RG \
    --cert-file ./selfsignedcert.crt \
    --gateway-name $APPGW_NAME \
    --name MySelfSignedTrustedRootCert
```

これで、次のコマンドを使用して、新しい (自己署名) ルート証明書を信頼するように HTTP 設定を更新できます。

```azurecli
az network application-gateway http-settings update \
    --resource-group $RG \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings \
    --root-certs MySelfSignedTrustedRootCert
```

---

### <a name="check-the-deployment-of-application-gateway"></a>Application Gateway のデプロイを確認する

作成後、次のコマンドを使用してバックエンドの正常性を確認します。 このコマンドの出力を使用すると、アプリケーション ゲートウェイがプライベート FQDN を介してアプリケーションに到達するかどうかを確認できます。

```azurecli
az network application-gateway show-backend-health \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP
```

次の例に示す出力は、バックエンド プールの正常な状態を示します。

```output
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="configure-dns-and-access-the-application"></a>DNS を構成してアプリケーションにアクセスする

次に、CNAME または A レコードを使用して、Application Gateway を指すようにパブリック DNS を構成します。 次のコマンドを使用して、Application Gateway のパブリック アドレスを見つけることができます。

```azurecli
az network public-ip show \
    --resource-group $RESOURCE_GROUP \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --query [ipAddress] \
    --output tsv
```

これで、パブリック ドメイン名を使用してアプリケーションにアクセスできるようになりました。

## <a name="see-also"></a>関連項目

- [VNET での Azure Spring Cloud のトラブルシューティング](./troubleshooting-vnet.md)
- [VNET での Azure Spring Cloud の実行に関するお客様の責任](./vnet-customer-responsibilities.md)

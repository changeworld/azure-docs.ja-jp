---
title: App Service Environment の証明書
description: App Service 環境の証明書に関連するトピックについて説明します。 ASE の単一テナントアプリで証明書バインドがどのように機能するかについて説明します。
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: a86a5e67faafc88c837f7401db473b0164125a82
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525610"
---
# <a name="certificates-and-the-app-service-environment"></a>証明書と App Service Environment 
> [!NOTE]
> これは、Isolated v2 App Service プランで使用される App Service Environment v3 に関する記事です
>

App Service Environment (ASE) は、ご使用の Azure Virtual Network 内で実行する Azure App Service のデプロイです。 これは、インターネットでアクセス可能なアプリケーション エンドポイントまたはご使用の仮想ネットワーク内にあるアプリケーション エンドポイントを使用してデプロイできます。 インターネットでアクセス可能なエンドポイントを使用して ASE をデプロイすると、そのデプロイは外部 ASE と呼ばれます。 仮想ネットワーク内にあるエンドポイントを使用して ASE をデプロイすると、そのデプロイは ILB ASE と呼ばれます。 ILB ASE の詳細については、[ILB ASE の作成と使用](./creation.md)に関するページを参照してください。

## <a name="application-certificates"></a>アプリケーション証明書

ASE でホストされているアプリは、マルチテナント App Service で利用できるアプリ中心の証明書機能を使用できます。 これには次のような機能が含まれます。

- SNI 証明書
- KeyVault がホストされている証明書

これらの証明書のアップロードおよび管理の要件と手順については、「[Azure App Service で TLS/SSL 証明書を追加する](../configure-ssl-certificate.md)」を参照してください。

証明書が App Service アプリまたは関数アプリに追加されたら、その証明書を使用して[カスタム ドメイン名をセキュリティで保護したり](../configure-ssl-bindings.md)、[その証明書をアプリケーション コードで使用したり](../configure-ssl-certificate-in-code.md)できます。

## <a name="tls-settings"></a>TLS の設定

[TLS の設定](../configure-ssl-bindings.md#enforce-tls-versions)は、アプリ レベルで構成できます。

## <a name="private-client-certificate"></a>クライアントのプライベート証明書

一般的なユース ケースでは、クライアント/サーバー モデルでのクライアントとしてアプリを構成します。 プライベート CA 証明書を使用してサーバーをセキュリティで保護する場合は、ご使用のアプリにクライアント証明書をアップロードする必要があります。 次の手順では、アプリが実行されているワーカーの truststore に証明書を読み込みます。 1 つのアプリに証明書を読み込むと、証明書をもう一度アップロードしなくても、同じ App Service プラン内にある他のアプリにもその証明書を使用できます。

>[!NOTE]
> プライベート クライアント証明書は、アプリの外部ではサポートされていません。 これにより、プライベート証明書を使用してレジストリからアプリ コンテナー イメージを取得するシナリオや、プライベート証明書を使用してフロントエンド サーバーを介して TLS 検証を行うシナリオなどでの使用が制限されます。

証明書 ( *.cer* ファイル) を ASE 内のアプリにアップロードするには、次の手順に従います。 *.cer* ファイル は、証明書からエクスポートできます。 テストを目的とした一時的な自己署名証明書を生成する PowerShell の例が最後に示されています。

1. Azure portal で証明書を必要とするアプリに移動します。
1. アプリで **[TLS/SSL 設定]** に移動します。 **[公開キー証明書 (.cer)]** をクリックします。 **[公開キー証明書のアップロード]** を選択します。 名前を指定します。 *.cer* ファイルを参照して選択します。 [アップロード] を選択します。 
1. サムプリントをコピーします。
1. **[アプリケーション設定]** に移動します。 サムプリントを値として使用して、アプリ設定 WEBSITE_LOAD_ROOT_CERTIFICATES を作成します。 複数の証明書がある場合は、次のように空白なしのコンマで区切ることで、同じ設定に配置することができます。 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

証明書は、その設定を構成したアプリと同じ App Service プラン内のすべてのアプリで使用できます。 別の App Service プラン内のアプリに対してもその証明書を使用できるようにする必要がある場合は、その App Service プランでアプリ設定操作を繰り返す必要があります。 証明書が設定されていることを確認するには、Kudu コンソールに移動し、PowerShell デバッグ コンソールで次のコマンドを発行します。

```azurepowershell-interactive
dir Cert:\LocalMachine\Root
```

テストを実行するには、自己署名証明書を作成し、次の PowerShell を使用して *.cer* ファイルを生成します。 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -CertStoreLocation "Cert:\LocalMachine\My" -DnsName "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "Cert:\LocalMachine\My\" + $certificate.Thumbprint
$fileName = "exportedcert.cer"
Export-Certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```

## <a name="next-steps"></a>次の手順

* [アプリケーション コードで証明書を使用する](../configure-ssl-certificate-in-code.md)方法に関する情報
---
title: Azure PowerShell による Azure Stack Edge Pro GPU の証明書を作成する | Microsoft Docs
description: Azure PowerShell コマンドレットを使用して、Azure Stack Edge Pro GPU デバイスの証明書を作成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: fc7ec5574e0f0223a66bc4e42ce9029db0ae4fc6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364075"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで証明書を使用する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure PowerShell コマンドレットを使用して独自の証明書を作成する手順について説明します。 この記事には、Azure Stack Edge デバイスに独自の証明書を持ち込む予定の場合に従う必要のあるガイドラインが含まれています。

証明書により、デバイスとそれにアクセスするクライアント間の通信を信頼できること、また適切なサーバーに暗号化された情報を送信していることが保証されます。 Azure Stack Edge デバイスが最初に構成されたときに、自己署名証明書が自動的に生成されます。 必要に応じて、独自の証明書を導入できます。 

次のいずれかの方法で、デバイスに独自の証明書を作成することができます。

 - Azure PowerShell コマンドレットを使用します。
 - Azure Stack Hub Readiness Checker ツールを使用して、証明機関から証明書を発行する際に役立つ証明書署名要求 (CSR) を作成します。 

この記事では、Azure PowerShell コマンドレットを使用して独自の証明書を作成する方法のみについて説明します。 

## <a name="prerequisites"></a>前提条件

独自の証明書を持ち込む前に、次のことを確認してください。

- [お使いの Azure Stack Edge デバイスで使用できる証明書の種類](azure-stack-edge-gpu-certificates-overview.md)を理解している。
- [証明書の種類ごとの証明書要件](azure-stack-edge-gpu-certificate-requirements.md)を確認済みである。


## <a name="create-certificates"></a>証明書の作成

次のセクションでは、署名チェーンとエンドポイント証明書を作成する手順について説明します。


### <a name="certificate-workflow"></a>証明書のワークフロー

環境内で動作するデバイスの証明書を作成するための定められた方法を持つことになります。 IT 管理者によって提供された証明書を使用できます。 

**開発またはテストの目的に限り、Windows PowerShell を使用して、ローカル システムで証明書を作成することもできます。** クライアントの証明書を作成するときは、次のガイドラインに従ってください。

1. 次の任意の種類の証明書を作成できます。

    - 単一の完全修飾ドメイン名 (FQDN) で使用できる単一の証明書を作成する。 たとえば、*mydomain.com* などです。
    - メイン ドメイン名と複数のサブドメインをセキュリティで保護するために、ワイルドカード証明書を作成する。 たとえば、* *.mydomain.com* などです。
    - 1 つの証明書で複数のドメイン名を対象とするサブジェクトの別名 (SAN) 証明書を作成する。 

2. 独自の証明書を導入する場合、署名チェーンのルート証明書が必要になります。 手順については、「[署名チェーン証明書を作成する](#create-signing-chain-certificate)」を参照してください。

3. 次に、アプライアンス、BLOB、Azure Resource Manager のエンドポイント証明書をローカル UI に対して作成できます。 アプライアンス、BLOB、Azure Resource Manager 用に 3 つの個別の証明書を作成することも、3 つのエンドポイントすべてに対して 1 つの証明書を作成することもできます。 詳細な手順については、[署名とエンドポイント証明書の作成](#create-signed-endpoint-certificates)に関するページを参照してください。

4. 3 つの個別の証明書を作成する場合も 1 つの証明書を作成する場合も、証明書の種類ごとに提供されているガイダンスに従って、サブジェクト名 (SN) とサブジェクトの別名 (SAN) を指定します。 

### <a name="create-signing-chain-certificate"></a>署名チェーン証明書を作成する

管理者モードで実行されている Windows PowerShell を使用して、この証明書を作成します。 **この方法で作成された証明書は、開発またはテストの目的のみに使用してください。**

署名チェーン証明書は、1 回だけ作成する必要があります。 他のエンドポイント証明書は、この証明書を参照して署名を確認します。
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>署名付きエンドポイント証明書を作成する

管理者モードで実行されている Windows PowerShell を使用して、この証明書を作成します。

以下の例では、次のデバイスに対してエンドポイント証明書を作成します。
    - デバイス名: `DBE-HWDC1T2`
    - DNS ドメイン: `microsoftdatabox.com`

ご使用のデバイスの名前と DNS ドメインに置き換えて、デバイスの証明書を作成してください。
 
**BLOB エンドポイント証明書**

個人用ストアに BLOB エンドポイントの証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager エンドポイント証明書**

個人用ストアに Azure Resource Manager エンドポイントの証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**デバイスのローカル Web UI 証明書**

個人用ストアにデバイスのローカル Web UI の証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**すべてのエンドポイント用の単一のマルチ SAN 証明書**

個人用ストアにすべてのエンドポイント用の単一の証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

証明書を作成したら、次の手順として、Azure Stack Edge Pro GPU デバイスに証明書をアップロードします

## <a name="next-steps"></a>次のステップ

[デバイスに証明書をアップロードします](azure-stack-edge-gpu-manage-certificates.md)。

---
title: ARM で ASE を作成する
description: Azure Resource Manager テンプレートを使用して、外部または ILB App Service Environment を作成する方法について説明します。
author: madsd
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 10/11/2021
ms.author: madsd
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 677703e455d985b91f77a71acce3f7809525b368
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519574"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して ASE を作成する

## <a name="overview"></a>概要
> [!NOTE]
> この記事は、Isolated App Service プランで使用される App Service Environment v2 と App Service Environment v3 に関するものです
> 

Azure App Service Environment (ASE) は、インターネットにアクセス可能なエンドポイントまたは Azure Virtual Network 内の内部アドレスのエンドポイントを使用して作成できます。 内部エンドポイントを使用して作成すると、内部ロード バランサー (ILB) と呼ばれる Azure コンポーネントによってそのエンドポイントが提供されます。 内部 IP アドレスの ASE は、ILB ASE と呼ばれます。 パブリック エンドポイントを持つ ASE は、外部 ASE と呼ばれます。 

ASE は、Azure Portal または Azure Resource Manager テンプレートを使用して作成できます。 この記事では、Resource Manager テンプレートを使用して外部 ASE または ILB ASE を作成するために必要な手順と構文について説明します。 Azure portal で ASEv2 を作成する方法については、[外部 ASE の作成][MakeExternalASE]に関するページ、または [ILB ASE の作成][MakeILBASE]に関するページを参照してください。
Azure portal で ASEv3 を作成する方法については、「[ASEv3 を作成する][Create ASEv3]」を参照してください。

Azure portal で ASE を作成する場合は、同時に仮想ネットワークを作成するか、またはデプロイする既存の仮想ネットワークを選択できます。 

テンプレートから ASE を作成するときは、以下が必要です。 

* Azure Virtual Network。
* その仮想ネットワーク内のサブネット。 推奨される ASE のサブネット サイズは、将来的な規模の拡大およびスケーリングのニーズに対応できる、256 のアドレスを持つ `/24` です。 ASE を作成した後は、サイズ変更はできません。
* 既存の仮想ネットワークとサブネットに ASE を作成する場合は、既存のリソース グループ名、仮想ネットワーク名、サブネット名が必要です。
* デプロイ先のサブスクリプション。
* デプロイ先の場所。

ASE の作成を自動化するには、下のセクションのガイドラインに従います。 カスタム dnsSuffix (例: `internal-contoso.com`) を使用して ILB ASEv2 を作成する場合は、さらにいくつかの操作を行います。

1. カスタム dnsSuffix を使用する ILB ASE を作成した後、ILB ASE ドメインに一致する TLS/SSL 証明書をアップロードする必要があります。

2. アップロードされた TLS/SSL 証明書は、"既定の" TLS/SSL 証明書として ILB ASE に割り当てられます。  この証明書は、ILB ASE 上のアプリが ASE に割り当てられた共通ルート ドメイン (`https://someapp.internal-contoso.com` など) を使用する場合に、そのアプリへの TLS/SSL トラフィックに使用されます。


## <a name="create-the-ase"></a>ASE を作成する
ASE とそれに関連するパラメーター ファイルを作成する Resource Manager テンプレートは、GitHub で入手できます ([ASEv3][asev3quickstarts] および [ASEv2][quickstartasev2create] 用)。

ASE を作成する場合は、これらの Resource Manager テンプレートの [ASEv3][asev3quickstarts] または [ASEv2][quickstartilbasecreate] の例を使用します。 これらのテンプレートは、ILB ASE の作成に対応します。 *azuredeploy.parameters.json* ファイルのほとんどのパラメーターは、ILB ASE と外部 ASE の作成に共通するパラメーターです。 以下の一覧では、既存のサブネットを使用して ILB ASE を作成する場合に特に注意が必要なパラメーターや、固有のパラメーターについて説明します。
### <a name="asev3-parameters"></a>ASEv3 のパラメーター
* *aseName*: 必須。 このパラメーターでは、一意の ASE 名を定義します。 
* *internalLoadBalancingMode*: 必須。 ほとんどの場合、これは 3 に設定します。これは、ポート 80/443 での HTTP と HTTPS の両方のトラフィックを意味します。 このプロパティを 0 に設定すると、HTTP/HTTPS トラフィックでは、パブリック VIP が引き続き使用されます。
* *zoneRedundant*: 必須。 ほとんどの場合、これは false に設定します。これにより、ASE は可用性ゾーン (AZ) にデプロイされません。 ゾーン ASE は一部のリージョンでデプロイできます。[こちら][AZ Support for ASEv3]を参照してください。
* *dedicatedHostCount*: 必須。 ほとんどの場合、これは 0 に設定します。これにより、専用ホストをデプロイせずに ASE が通常通りデプロイされます。
* *useExistingVnetandSubnet*: 必須。 既存の仮想ネットワークとサブネットを使用する場合は、true に設定します。 
* *vNetResourceGroupName*: 既存の仮想ネットワークとサブネットを使用している場合は必須です。 このパラメーターでは、ASE を配置する既存の仮想ネットワークとサブネットのリソース グループ名を定義します。
* *virtualNetworkName*: 既存の仮想ネットワークとサブネットを使用している場合は必須です。 このパラメーターでは、ASE を配置する既存の仮想ネットワークとサブネットの仮想ネットワーク名を定義します。
* *subnetName*: 既存の仮想ネットワークとサブネットを使用している場合は必須です。 このパラメーターでは、ASE を配置する既存の仮想ネットワークとサブネットのサブネット名を定義します。
* *createPrivateDNS*: ASEv3 の作成後にプライベート DNS ゾーンを作成する場合は true に設定します。 ILB ASE の場合、このパラメーターを true に設定すると、*appserviceenvironment.net* DNS サフィックスが付加された ASE 名としてプライベート DNS ゾーンが作成されます。 
### <a name="asev2-parameters"></a>ASEv2 のパラメーター
* *aseName*: このパラメーターでは、一意の ASE 名を定義します。
* *location*: このパラメーターでは、App Service Environment の場所を定義します。
* *existingVirtualNetworkName*: このパラメーターでは、ASE を配置する既存の仮想ネットワークとサブネットの仮想ネットワーク名を定義します。
* *existingVirtualNetworkResourceGroup*: このパラメーターでは、ASE を配置する既存の仮想ネットワークとサブネットのリソース グループ名を定義します。
* *subnetName*: このパラメーターでは、ASE を配置する既存の仮想ネットワークとサブネットのサブネット名を定義します。
* *internalLoadBalancingMode*:ほとんどの場合、これは 3 に設定します。この設定により、ポート 80/443 の HTTP/HTTPS トラフィックと、ASE 上の FTP サービスによってリッスンされているコントロール/データ チャネル ポートは、ILB が割り当てられた仮想ネットワークの内部アドレスにバインドされます。 このプロパティを 2 に設定すると、FTP サービスに関連するポート (コントロール チャネルとデータ チャネルの両方) のみが ILB アドレスにバインドされます。 このプロパティを 0 に設定すると、HTTP/HTTPS トラフィックでは、パブリック VIP が引き続き使用されます。
* *dnsSuffix*:このパラメーターでは、ASE に割り当てられる既定のルート ドメインを定義します。 Azure App Service のパブリック版では、すべての Web アプリの既定のルート ドメインは *azurewebsites.net* です。 ILB ASE はユーザーの仮想ネットワークの内部にあるため、パブリック サービスの既定のルート ドメインを使用しても意味がありません。 ILB ASE には、会社の内部仮想ネットワーク内での使用に適した既定のルート ドメインを用意する必要があります。 たとえば、Contoso Corporation では、Contoso の仮想ネットワーク内でのみ解決およびアクセス可能になるよう設計されたアプリに対して、*internal-contoso.com* という既定のルート ドメインを使用できます。 
* *ipSslAddressCount*:ILB ASE には単一の ILB アドレスしかないため、このパラメーターは、*azuredeploy.json* ファイル内で自動的に既定値の 0 に設定されます。 ILB ASE 向けの明示的な IP SSL アドレスはありません。 そのため、ILB ASE の IP SSL アドレス プールは、0 に設定する必要があります。 それ以外の場合、プロビジョニングのエラーが発生します。

*azuredeploy.parameters.json* ファイルに入力した後で、PowerShell コード スニペットを使用して、ASE を作成します。 ファイル パスは、コンピューター上の Resource Manager テンプレート ファイルの場所に一致するように変更してください。 Resource Manager のデプロイ名とリソース グループ名に独自の値を指定することも忘れずに行ってください。

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

ASE の作成には 2 時間ほどかかります。 続いて、ポータルの、デプロイをトリガーしたサブスクリプションの ASE の一覧に、作成した ASE が表示されます。

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>"既定の" TLS/SSL 証明書をアップロードして構成する
TLS/SSL 証明書は、アプリへの TLS/SSL 接続を確立するために使用する "既定の" TLS/SSL 証明書として ASE に関連付ける必要があります。 ASE の既定の DNS サフィックスが *internal-contoso.com* である場合、`https://some-random-app.internal-contoso.com` への接続には * *.internal-contoso.com* に対して有効な TLS/SSL 証明書が必要です。 

内部証明機関を利用する、外部の発行者から証明書を購入する、自己署名証明書を使用する、のいずれかの手段で有効な TLS/SSL 証明書を取得します。 どのようなソースから TLS/SSL 証明書を取得した場合でも、以下の証明書属性を適切に構成する必要があります。

* **Subject**:この属性は、* *.your-root-domain-here.com* に設定する必要があります。
* **Subject Alternative Name**:この属性には、* *.your-root-domain-here.com* と * *.scm.your-root-domain-here.com* の両方が含まれている必要があります。 各アプリに関連付けられた SCM/Kudu サイトへの TLS 接続で、*your-app-name.scm.your-root-domain-here.com* 形式のアドレスが使用されます。

有効な TLS/SSL 証明書を取得した後、さらに 2 つの準備手順を実行する必要があります。 TLS/SSL 証明書を .pfx ファイルとして変換/保存します。 .pfx ファイルには、すべての中間証明書とルート証明書を忘れずに含めてください。 パスワードで保護してください。

.pfx ファイルは base64 文字列に変換する必要がありますが、これは TLS/SSL 証明書が Resource Manager テンプレートを使用してアップロードされるためです。 Resource Manager テンプレートはテキスト ファイルであるため、.pfx ファイルを base64 文字列に変換する必要があります。 この方法で、テンプレートのパラメーターとして含めることができます。

以下の PowerShell コード スニペットを使用して、次のことを行います。

* 自己署名証明書を作成します。
* .pfx ファイルとして証明書をエクスポートします。
* .pfx ファイルを base64 でエンコードされた文字列に変換します。
* base64 でエンコードされた文字列を別のファイルに保存します。 

この base64 エンコード用の PowerShell コードは、[PowerShell スクリプトのブログ][examplebase64encoding]に記載されているコードを基にしています。

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

TLS/SSL 証明書が正常に生成され、base64 でエンコードされた文字列に変換されたら、GitHub にある、[既定の SSL 証明書を構成する][quickstartconfiguressl]ための Resource Manager テンプレートのサンプルを使用します。 

*azuredeploy.parameters.json* ファイル内の各パラメーターを以下に示します。

* *appServiceEnvironmentName*:構成対象の ILB ASE の名前。
* *existingAseLocation*:ILB ASE のデプロイ先の Azure リージョンを含むテキスト文字列。  次に例を示します。"South Central US"。
* *pfxBlobString*:based64 でエンコードされた .pfx ファイルの文字列表現。 前述のコード スニペットを使用し、"exportedcert.pfx.b64" に含まれる文字列をコピーします。 *pfxBlobString* 属性の値として貼り付けます。
* *password*:pfx ファイルの保護に使用されるパスワード。
* *certificateThumbprint*:証明書のサムプリント。 この値 (前述のコード スニペットにある *$certificate.Thumbprint* など) を PowerShell から取得した場合、値はそのまま使用できます。 この値を Windows 証明書のダイアログ ボックスからコピーした場合は、忘れずに余分なスペースを削除してください。 *certificateThumbprint* は、AF3143EB61D43F6727842115BB7F17BBCECAECAE のようになります。
* *certificateName*:証明書の識別に使用される、独自に選択したわかりやすい文字列識別子。 この名前は、TLS/SSL 証明書を表す *Microsoft.Web/certificates* エンティティで、一意の Resource Manager 識別子の一部として使用されます。 この名前は、サフィックス \_yourASENameHere_InternalLoadBalancingASE で終わる *必要があります*。 このサフィックスは、ILB が有効な ASE を保護するためにこの証明書が使用されることを示す標識として、Azure Portal で使用されます。

一部省略した *azuredeploy.parameters.json* の例を次に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

*azuredeploy.parameters.json* ファイルに入力した後、PowerShell コード スニペットを使用して、既定の TLS/SSL 証明書を構成します。 ファイル パスは、コンピューター上の Resource Manager テンプレート ファイルの場所に一致するように変更してください。 Resource Manager のデプロイ名とリソース グループ名に独自の値を指定することも忘れずに行ってください。

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

変更を適用するには ASE フロントエンドあたり約 40 分かかります。 たとえば、2 つのフロントエンドを使用する既定サイズの ASE では、テンプレートが完了するまでに約 1 時間 20 分かかります。 テンプレートの実行中に、ASE をスケーリングすることはできません。  

テンプレートが完了したら、ILB ASE 上のアプリは、HTTPS 経由でアクセスできます。 接続は、既定の TLS/SSL 証明書を使用して保護されます。 既定の TLS/SSL 証明書は、アプリケーション名と既定のホスト名の組み合わせを使用して ILB ASE 上のアプリが指定された場合に使用されます。 たとえば、`https://mycustomapp.internal-contoso.com` の場合、* *.internal-contoso.com* の既定の TLS/SSL 証明書が使用されます。

ただし、パブリックのマルチテナント サービスで実行されるアプリの場合と同じように、開発者は個々のアプリのカスタム ホスト名を構成できます。 個々のアプリに一意の SNI TLS/SSL 証明書バインドを構成することもできます。

<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/resources/templates/web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/resources/templates/web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/resources/templates/web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/resources/templates/web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/resources/templates/web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: ./integrate-with-application-gateway.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
[Create ASEv3]: creation.md
[asev3quickstarts]: https://azure.microsoft.com/resources/templates/web-app-asp-app-on-asev3-create
[AZ Support for ASEv3]: zone-redundancy.md
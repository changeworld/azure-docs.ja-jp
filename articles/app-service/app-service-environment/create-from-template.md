---
title: "Resource Manager テンプレートを使用した Azure App Service Environment の作成"
description: "Resource Manager テンプレートを使用して、外部または ILB Azure App Service Environment を作成する方法について説明します。"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して ASE を作成する方法

## <a name="overview"></a>概要
App Service Environment (ASE) は、インターネットでアクセス可能なエンドポイントまたは Azure Virtual Network (VNet) の内部アドレスのエンドポイントを使用して作成できます。  内部エンドポイントを使用して作成すると、内部ロード バランサー (ILB) と呼ばれる Azure コンポーネントによってそのエンドポイントが提供されます。  パブリック エンドポイントを使用する ASE は外部 ASE と呼ばれ、内部 IP アドレスの ASE は ILB ASE と呼ばれます。  

ASE は、Azure Portal または Azure Resource Manager テンプレートを使用して作成できます。  この記事では、Azure Resource Manager テンプレートを使用して外部 ASE または ILB ASE を作成するために必要な手順と構文について説明します。  ポータルで ASE を作成する方法の詳細については、[外部 ASE の作成][MakeExternalASE]に関する記事または [ILB ASE の作成][MakeILBASE]に関する記事をご覧ください。

ポータルで ASE を作成するときに、同時に VNet を作成するか、既存の VNet にデプロイするかを選択できます。  テンプレートを使用して作成するときは、以下が必要です。 
* Resource Manager VNet。
* その VNet のサブネット。  将来の成長に対応できるように、ASE サブネットは 128 個のアドレスを持つ /25 にすることをお勧めします。  ASE の作成後にこれを変更することはできません。
* VNet のリソース ID。  これは、Azure Portal の Azure Virtual Network のプロパティで取得できます。
* デプロイ先のサブスクリプション。
* デプロイ先の場所。

ASE の作成を自動化するには、次の手順に従います。

1. テンプレートを使用して ASE を作成します。  外部 ASE を作成する場合、これは 1 つの手順で完了します。  ILB ASE を作成する場合は、追加の手順が必要になります。
2. ILB ASE を作成したら、ILB ASE ドメインに一致する SSL 証明書がアップロードされます。
3. アップロードされた SSL 証明書は、"既定の" SSL 証明書として ILB ASE に割り当てられます。  この SSL 証明書は、ASE に割り当てられた共通ルート ドメインを使用してアプリがアドレス指定された場合 (https://someapp.mycustomrootcomain.com など) に、ILB ASE 上のアプリへの SSL トラフィックに使用されます。


## <a name="creating-the-ase"></a>ASE の作成
ASE および関連するパラメーター ファイルを作成するサンプルの Azure Resource Manager テンプレートは、GitHub の[こちら][quickstartasev2create]で入手できます。

ILB ASE を作成する場合は、[こちら][quickstartilbasecreate]の Resource Manager テンプレートを使用する必要があります。  これらのテンプレートは、ILB ASE の作成に対応します。  *azuredeploy.parameters.json* ファイルのほとんどのパラメーターは、ILB ASE と外部 ASE の両方の作成に共通するパラメーターです。  以下の一覧では、ILB ASE を作成するうえで特に注意が必要なパラメーターや、固有のパラメーターについて説明します。

* *interalLoadBalancingMode*: ほとんどの場合、これは 3 に設定します。この設定により、ポート 80/443 の HTTP/HTTPS トラフィックと、ASE 上の FTP サービスによってリッスンされているコントロール/データ チャネル ポートは、ILB が割り当てられた仮想ネットワーク内部アドレスにバインドされます。  このプロパティを 2 に設定すると、FTP サービス関連のポート (コントロール チャネルとデータ チャネル両方のポート) のみが ILB アドレスにバインドされ、HTTP/HTTPS トラフィックにはパブリック VIP が使用されます。
* *dnsSuffix*: このパラメーターでは、ASE に割り当てられる既定のルート ドメインを定義します。  Azure App Service のパブリック版では、すべての Web アプリの既定のルート ドメインは *azurewebsites.net*です。  しかし、ILB ASE はユーザーの仮想ネットワーク内部に位置することから、パブリック サービスの既定のルート ドメインを使用しても意味がありません。  ILB ASE には、会社の内部仮想ネットワーク内での使用に適した既定のルート ドメインを用意する必要があります。  たとえば、Contoso Corporation という架空の企業では、Contoso の仮想ネットワーク内でのみ解決およびアクセス可能になるよう設計されたアプリに対して、 *internal-contoso.com* という既定のルート ドメインが使用されます。 
* *ipSslAddressCount*: ILB ASE には単一の ILB アドレスしかないため、このパラメーターは、*azuredeploy.json* ファイル内で既定値の 0 に自動設定されます。  ILB ASE 用に明示されている IP-SSL アドレスがないため、ILB ASE の IP-SSL アドレス プールはゼロに設定する必要があり、ゼロ以外に設定するとプロビジョニング エラーが発生します。 

*azuredeploy.parameters.json* ファイルへの入力が完了したら、次の PowerShell コード スニペットを使用して ASE を作成できます。  ファイル パス ("PATH" 部分) は、コンピューター上の Azure Resource Manager テンプレート ファイルの場所に一致するように変更してください。  また、Azure Resource Manager のデプロイ名とリソース グループ名に独自の値を指定することも忘れずに行ってください。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure Resource Manager テンプレートの送信後、ASE が作成されるまで約 1 時間かかります。  作成が完了すると、ポータル UX で、デプロイをトリガーしたサブスクリプションの App Service Environment の一覧に ASE が表示されます。

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>"既定の" SSL 証明書のアップロードと構成
ILB ASE を作成したら、SSL 証明書を、アプリへの SSL 接続を確立するために使用する "既定の" SSL証明書として ASE に関連付ける必要があります。  ここでも架空の Contoso Corporation の例で考えると、ASE の既定の DNS サフィックスが *internal-contoso.com* である場合、*https://some-random-app.internal-contoso.com* への接続には **.internal-contoso.com* に対して有効な SSL 証明書が必要です。 

有効な SSL 証明書を取得するには、内部 CA を利用する、外部の発行元から証明書を購入する、自己署名証明書を使用するなどの方法があります。  どのようなソースから SSL 証明書を取得した場合でも、以下の証明書属性を適切に構成する必要があります。

* *Subject*: この属性は、**.your-root-domain-here.com* に設定する必要があります。
* *Subject Alternative Name*: この属性には、**.your-root-domain-here.com* と **.scm.your-root-domain-here.com* の両方が含まれている必要があります。  2 つ目のエントリが必要な理由は、各アプリに関連付けられた SCM/Kudu サイトへの SSL 接続が、 *your-app-name.scm.your-root-domain-here.com*形式のアドレスを使用して確立されるためです。

有効な SSL 証明書を取得した後は、さらに 2 つの準備手順を実行する必要があります。  SSL 証明書は、.pfx ファイルに変換して保存する必要があります。  .pfx ファイルには、中間証明書とルート証明書をすべて含める必要があり、さらにパスワードで保護する必要もあるという点に注意してください。

その後、.pfx ファイルは base64 文字列に変換する必要がありますが、これは SSL 証明書が Azure Resource Manager テンプレートを使用してアップロードされるためです。  Azure Resource Manager テンプレートはテキスト ファイルであるため、パラメーターとしてテンプレートに含めることができるように、.pfx ファイルを base64 文字列に変換する必要があります。

以下の Powershell コード スニペットには、自己署名証明書を生成して、その証明書を .pfx ファイルとしてエクスポートし、.pfx ファイルを base64 でエンコードされた文字列に変換してから、それを別のファイルに保存する例を示します。  base64 エンコード用の PowerShell コードは、[PowerShell スクリプトのブログ][examplebase64encoding]に記載されているコードを基にしています。

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

SSL 証明書が正常に生成され、base64 でエンコードされた文字列に変換されたら、GitHub にある、[既定の SSL 証明書を構成する][quickstartconfiguressl]ためのサンプルの Azure Resource Manager テンプレートを使用できます。

*azuredeploy.parameters.json* ファイル内の各パラメーターを以下に示します。

* *appServiceEnvironmentName*: 構成対象の ILB ASE の名前。
* *existingAseLocation*: ILB ASE のデプロイ先の Azure リージョンを含むテキスト文字列。  たとえば、"South Central US" のようになります。
* *pfxBlobString*: based64 でエンコードされた .pfx ファイルの文字列表現。  前述したコード スニペットを使用すると、"exportedcert.pfx.b64" 内に含まれる文字列をコピーし、 *pfxBlobString* 属性の値として貼り付けることになります。
* *password*: .pfx ファイルの保護に使用されるパスワード。
* *certificateThumbprint*: 証明書の拇印。  この値を Powershell (前述のコード スニペットにある *$certificate.Thumbprint* など) から取得した場合、値はそのまま使用できます。  ただし、この値を Windows 証明書のダイアログからコピーした場合は、忘れずに余分なスペースを削除してください。  *certificateThumbprint* は、AF3143EB61D43F6727842115BB7F17BBCECAECAE のようになります。
* *certificateName*: 証明書の識別に使用される、独自に選択したわかりやすい文字列識別子。  この名前は、SSL 証明書を表す *Microsoft.Web/certificates* エンティティに固有の Azure Resource Manager 識別子の一部として使用されます。  この名前は、サフィックス "\_yourASENameHere_InternalLoadBalancingASE" で終わる**必要があります**。  このサフィックスは、ILB が有効な ASE を保護するためにこの証明書が使用されることを示すインジケーターとして、ポータルによって使用されます。

一部省略した *azuredeploy.parameters.json* の例を次に示します。

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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

*azuredeploy.parameters.json* ファイルへの入力が完了したら、以下の PowerShell コード スニペットを使用して既定の SSL 証明書を構成することができます。  ファイル パス ("PATH" 部分) は、コンピューター上の Azure Resource Manager テンプレート ファイルの場所に一致するように変更してください。  また、Azure Resource Manager のデプロイ名とリソース グループ名に独自の値を指定することも忘れずに行ってください。

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure Resource Manager テンプレートを送信した後、変更が適用されるまでには、ASE フロントエンドあたり約 40 分かかります。  たとえば、2 つのフロントエンドを使用する既定サイズの ASE では、テンプレートが完了するまで約 1 時間 20 分かかります。  テンプレートの実行中に、ASE をスケーリングすることはできません。  

テンプレートが完了すると、ILB ASE 上のアプリに HTTPS 経由でアクセスできるようになり、接続は既定の SSL 証明書を使用して保護されます。  既定の SSL 証明書は、ILB ASE 上のアプリが、アプリケーション名と既定のホスト名の組み合わせを使用してアドレス指定された場合に使用されます。  たとえば、*https://mycustomapp.internal-contoso.com* と指定された場合は、**.internal-contoso.com* の既定の SSL 証明書が使用されます。

ただし、パブリック マルチテナント サービスで実行されているアプリと同様に、開発者が個々のアプリに対してカスタム ホスト名を設定し、各アプリに固有の SNI SSL 証明書バインドを構成することもできます。  

## <a name="asev1"></a>ASEv1 ##
App Service Environment には、ASEv1 と ASEv2 の 2 つのバージョンがあります。 前述の情報は ASEv2 を中心としていました。 このセクションでは、ASEv1 と ASEv2 の違いについて説明します。

ASEv1 では、すべてのリソースを手動で管理する必要があります。 これには、フロントエンド、worker、IP ベースの SSL に使用する IP アドレスが含まれます。 App Service プランをスケールアウトするには、そのプランをホストする worker プールを先にスケールアウトしておく必要があります。

ASEv1 では、ASEv2 とは異なる価格モデルを使用します。 ASEv1 では、割り当てられたコアごとに料金を支払う必要があります。 これには、フロントエンドまたはどのワークロードもホストしていない worker に使用されるコアが含まれます。 ASEv1 では、App Service Environment の既定の最大スケール サイズは、合計 55 個のホストです。 これには worker とフロントエンドが含まれます。 ASEv1 の利点の 1 つは、クラシック仮想ネットワークと Resource Manager 仮想ネットワークにデプロイできることです。 ASEv1 の詳細については、[App Service Environment v1 の概要][ASEv1Intro]に関する記事をご覧ください。

Resource Manager テンプレートを使用して ASEv1 を作成する場合は、[Resource Manager テンプレートを使用した ILB ASE v1 の作成][ILBASEv1Template]に関する記事をご覧ください。


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md


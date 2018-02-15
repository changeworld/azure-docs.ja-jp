---
title: "Azure Stack とデータセンターの統合 - DNS"
description: "Azure Stack の DNS をデータセンターの DNS と統合する方法について説明します。"
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 504cbabe6ea4b7ad71601186dac853515f8c4709
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---dns"></a>Azure Stack とデータセンターの統合 - DNS
Azure Stack の外部から Azure Stack エンドポイント (`portal`、`adminportal`、`management`、`adminmanagement` など) にアクセスできるようにするには、Azure Stack DNS サービスを、Azure Stack で使用したい DNS ゾーンをホストする DNS サーバーと統合する必要があります。

## <a name="azure-stack-dns-namespace"></a>Azure Stack の DNS 名前空間
Azure Stack をデプロイするときに、DNS に関するいくつかの重要な情報を入力する必要があります。


|フィールド  |[説明]  |例|
|---------|---------|---------|
|リージョン|Azure Stack のデプロイの地理的な場所。|`east`|
|外部ドメイン名|Azure Stack のデプロイに使用したいゾーンの名前。|`cloud.fabrikam.com`|
|内部ドメイン名|Azure Stack のインフラストラクチャ サービスに使用される内部ゾーンの名前。  これは、ディレクトリ サービスと統合されたプライベートなゾーンです (Azure Stack のデプロイの外部からは到達できません)。|`azurestack.local`|
|DNS フォワーダ|Azure Stack の外部 (企業イントラネットまたはパブリック インターネット上) でホストされている DNS クエリ、DNS ゾーンおよびレコードを転送するために使用される DNS サーバー。|`10.57.175.34`<br>`8.8.8.8`|
|名前付けのプレフィックス (省略可能)|Azure Stack インフラストラクチャ ロール インスタンス マシン名に使用する名前付けのプレフィックス。  指定されていない場合、既定値は`azs` です。|`azs`|

Azure Stack のデプロイの完全修飾ドメイン名 (FQDN) とエンドポイントは、リージョン パラメーターと外部ドメイン名 パラメーターの組み合わせです。 前の表に示した例の値を使用すると、この Azure Stack のデプロイの FQDN は次の名前のようになります。

`east.cloud.fabrikam.com`

同様に、このデプロイのエンドポイントは次の URL のようになります。

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

この例の DNS 名前空間を Azure Stack のデプロイに使用するには、次の条件を満たしている必要があります。

- 使用している名前解決の要件に応じて、ゾーン `fabrikam.com` がドメイン レジストラーまたは社内の DNS サーバー (あるいはその両方) に登録されている。
- 子ドメイン `cloud.fabrikam.com` がゾーン`fabrikam.com` の下に存在する。
- ゾーン `fabrikam.com` および `cloud.fabrikam.com` をホストする DNS サーバーに Azure Stack のデプロイから到達できる。

Azure Stack の外部から Azure Stack エンドポイントおよびインスタンスの DNS 名を解決できるようにするには、Azure Stack の外部 DNS ゾーンをホストする DNS サーバーと、使用したい親ゾーンをホストする DNS サーバーを統合する必要があります。


## <a name="resolution-and-delegation"></a>解決と委任

DNS サーバーには次の 2 種類があります。

- 権限のある DNS サーバーは、DNS ゾーンをホストします。 このサーバーは、これらのゾーン内のレコードに対する DNS クエリのみに応答します。
- 再帰 DNS サーバーは、DNS ゾーンをホストしません。 このサーバーは、権限のある DNS サーバーを呼び出して必要なデータを収集することで、すべての DNS クエリに応答します。

Azure Stack には、権限のある DNS サーバーと再帰 DNS サーバーの両方が含まれます。 再帰サーバーは、その Azure Stack のデプロイの内部プライベート ゾーンと外部パブリック DNS ゾーンを除くすべての名前の解決に使用されます。 

![Azure Stack DNS のアーキテクチャ](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Azure Stack からの外部 DNS 名の解決

Azure Stack 外部のエンドポイントの DNS 名 (たとえば www.bing.com) を解決するには、Azure Stack が権限のない DNS 要求の転送に使用できる DNS サーバーを提供する必要があります。 デプロイでは、Azure Stack の要求の転送先となる DNS サーバーをデプロイ ワークシート ("DNS フォワーダー" フィールド) に入力する必要があります。 フォールト トレランスのために、このフィールドには 2 つ以上のサーバーを入力します。 これらの値が入力されない場合、Azure Stack のデプロイは失敗します。

### <a name="configure-conditional-dns-forwarding"></a>条件付き DNS フォワーダーの構成

> [!IMPORTANT]
> この要件が該当するのは AD FS デプロイのみです。

既存の DNS インフラストラクチャ を使用して名前解決を有効にするには、条件付きフォワーダーを構成します。

条件付きフォワーダーを追加するには、特権エンドポイントを使用する必要が あります。

この手順では、データセンター ネットワーク内の、Azure Stack の特権エンドポイントと通信できるコンピューターを使用します。

1. 管理者特権での Windows PowerShell セッション (管理者として実行) を開き、特権エンドポイントの IP アドレスに接続します。 CloudAdmin 認証の資格情報を使用します。

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. 特権エンドポイントに接続したら、次の PowerShell コマンドを実行します。 サンプルの値を、使用する DNS サーバーのドメイン名とアドレスで置き換えてください。

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Azure Stack 外部からの DNS 名の解決
権限のあるサーバーは、外部の DNS ゾーンとユーザーが作成したゾーンの情報を保持しています。 このサーバーと統合することで、ゾーンの委任または条件付き転送を使用して Azure Stack 外部からの Azure Stack DNS 名を解決できるようになります。

## <a name="get-dns-server-external-endpoint-information"></a>DNS サーバーの外部エンドポイント情報の取得

Azure Stack のデプロイを DNS インフラストラクチャと統合するには、次の情報が必要です。

- DNS サーバーの FQDN
- DNS サーバーの IP アドレス

Azure Stack DNS サーバーの FQDN は次のような形式をとります。

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

サンプルの値を使用すると、DNS サーバーの FQDN は次のようになります。

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


この情報は、すべての Azure Stack のデプロイの `AzureStackStampDeploymentInfo.json` という名前のファイルの末尾にも作成されます。 このファイルは、デプロイ仮想マシンの `C:\CloudDeployment\logs` フォルダー内にあります。 Azure Stack のデプロイに使用された値がわからない場合は、ここから取得できます。

デプロイ仮想マシンが使用不可またはアクセス不可になっている場合は、特権エンドポイントに接続して `Get-AzureStackInfo` PowerShell コマンドレットを実行することで値を取得できます。 特権エンドポイントの詳細については、(ここに記事のリンクを追加) をご覧ください。

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Azure Stack への条件付き転送の設定

Azure Stack と DNS インフラストラクチャを統合する最も簡単で安全な方法は、親ゾーンをホストするサーバーから、ゾーンの条件付き転送を行うことです。 Azure Stack の外部 DNS 名前空間の親ゾーンをホストする DNS サーバーを直接制御できる場合は、この方法をお勧めします。

DNS で条件付き転送を行う方法がわからない場合は、TechNet の記事「[Assign a Conditional Forwarder for a Domain Name (ドメイン名の条件付きフォワーダを割り当てる)](https://technet.microsoft.com/library/cc794735)」またはお使いの DNS ソリューションに固有のドキュメントをご覧ください。

会社のドメイン名の子ドメインのように見える Azure Stack 外部の DNS ゾーンを指定しているシナリオでは、条件付き転送は使用できません。 DNS 委任を構成する必要があります。

例:

- 会社の DNS ドメイン名: `contoso.com`
- Azure Stack 外部の DNS ドメイン名: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>外部 DNS ゾーンの Azure Stack への委任

DNS 名を Azure Stack デプロイの外部から解決できるようにするには、DNS 委任を設定する必要があります。

各レジストラーは独自の DNS 管理ツールを所有していて、ドメインのネーム サーバー レコードを変更します。 レジストラーの DNS 管理ページで、NS レコードを編集し、ゾーンの NS レコードを、Azure Stack の NS レコードに置き換えます。

ほとんどの DNS レジストラーでは、委任を実行するために 2 つ以上の DNS サーバーを指定する必要があります。

## <a name="next-steps"></a>次の手順

[ファイアウォールの統合](azure-stack-firewall.md)

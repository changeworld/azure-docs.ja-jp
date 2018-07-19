---
title: Azure Stack 統合システムの Azure Stack 公開キー インフラストラクチャ証明書の要件 | Microsoft Docs
description: Azure Stack 統合システムの Azure Stack PKI 証明書のデプロイ要件について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 13bc82caf5e10f5b35df29d085349ec4c80628a2
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929272"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack 公開キー インフラストラクチャ証明書の要件

Azure Stack には、少数の Azure Stack サービスやテナント VM に割り当てられた外部からアクセス可能なパブリック IP アドレスを使用するパブリック インフラストラクチャ ネットワークが存在します。 Azure Stack のデプロイ中に、これらの Azure Stack パブリック インフラストラクチャ エンドポイントの適切な DNS 名を持つ PKI 証明書が必要です。 この記事では、次の項目に関する情報を提供します。

- Azure Stack をデプロイするために必要な証明書の種類
- これらの仕様に一致する証明書を取得するプロセス
- デプロイ中にこれらの証明書を準備、検証、および使用する方法

> [!Note]  
> デプロイ時には、その対象となる ID プロバイダー (Azure AD または AD FS) に合ったデプロイ フォルダーに証明書をコピーする必要があります。 すべてのエンドポイントについて 1 つの証明書を使用する場合は、後述の表に記載した各デプロイ フォルダーに、その証明書ファイルをコピーしてください。 フォルダー構造は、デプロイ仮想マシンにあらかじめ作成されており、C:\CloudDeployment\Setup\Certificates で確認できます。 

## <a name="certificate-requirements"></a>証明書の要件
次の一覧では、Azure Stack をデプロイするために必要な証明書の要件について説明します。 
- 証明書は、内部の証明機関または公的証明機関のどちらかから発行されている必要があります。 公的証明機関が使用されている場合は、Microsoft の信頼されたルート機関プログラムの一部として基本オペレーティング システム イメージに含まれている必要があります。 詳細な一覧については、https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca をご覧ください。 
- お使いの Azure Stack インフラストラクチャは、証明書において公開されている証明機関の証明書失効リスト (CRL) の場所にネットワーク アクセスできる必要があります。 この CRL は、http エンドポイントである必要があります
- 証明書を交換する場合、証明書は、デプロイ時に指定された証明書の署名に使用したのと同じ内部の証明機関、または上記の公的パブリック証明機関のいずれかから発行されたものである必要があります。
- 自己署名証明書は使用できません。
- デプロイおよびローテーションの場合は、証明書のサブジェクト名フィールドとサブジェクトの別名 (SAN) フィールドのすべての名前空間をカバーする 1 つの証明書を使用するか、または、各名前空間で利用する予定の Azure Stack サービスが必要とする個別の証明書を使用することができます。 注: どちらの方法でも、必要とされるエンドポイントに対してワイルドカードを使用する必要があります (例: **KeyVault** と **KeyVaultInternal**)。 
- 証明書署名アルゴリズムを SHA1 にすることはできません。より強力にする必要があります。 
- Azure Stack のインストールには公開キーと秘密キーの両方が必要なため、証明書の形式は PFX である必要があります。 
- 証明書 pfx ファイルの "Key Usage" フィールドには、"Digital Signature" と "KeyEncipherment" の値が含まれている必要があります。
- 証明書の pfx ファイルの "Enhanced Key Usage" フィールドには、"Server Authentication (1.3.6.1.5.5.7.3.1)" と "Client Authentication (1.3.6.1.5.5.7.3.2)" の値が含まれている必要があります。
- 証明書の "Issued to:" フィールドは "Issued by:" フィールドと同じにしないでください。
- デプロイの時点で、すべての証明書 pfx ファイルのパスワードが同じである必要があります。
- 証明書 pfx に対するパスワードは、複雑なパスワードである必要があります。
- サブジェクト名と、サブジェクトの別名の拡張子 (x509v3_config) のサブジェクトの別名が一致するようにします。 サブジェクトの別名フィールドでは、単一の SSL 証明書によって保護される追加のホスト名 (Web サイト、IP アドレス、共通名) を指定できます。

> [!NOTE]  
> 自己署名証明書は使用できません。

> [!NOTE]  
> 証明書の信頼チェーン内での中間証明機関の存在がサポートされています。 

## <a name="mandatory-certificates"></a>必須の証明書
このセクションの表では、Azure AD と AD FS Azure Stack の両方のデプロイに必要な Azure Stack パブリック エンドポイント PKI 証明書について説明します。 証明書の要件が領域のほか、使用される名前空間や、名前空間ごとに必要な証明書でグループ化されています。 この表ではまた、ソリューション プロバイダーがパブリック エンドポイントごとに異なる証明書をコピーするフォルダーについても説明します。 

各 Azure Stack パブリック インフラストラクチャ エンドポイントの適切な DNS 名を持つ証明書が必要です。 各エンドポイントの DNS 名は、*&lt;prefix>.&lt;region>.&lt;fqdn>* の形式で表されます。 

デプロイでは、[region] と [externalfqdn] の値が Azure Stack システム用に選択したリージョン名と外部ドメイン名に一致している必要があります。 例として、リージョン名が *Redmond* で、外部ドメイン名が *contoso.com* である場合、DNS 名は *&lt;prefix>.redmond.contoso.com* の形式になります。 *&lt;prefix>* 値は、その証明書によってセキュリティ保護されるエンドポイントを指定するようにマイクロソフトによって事前に設計されています。 さらに、外部インフラストラクチャ エンドポイントの *&lt;prefix>* 値は、特定のエンドポイントを使用する Azure Stack サービスによって異なります。 

> [!note]  
> 証明書は、すべてのディレクトリにコピーされるサブジェクト フィールドおよびサブジェクトの別名 (SAN) フィールドのすべての名前空間をカバーする 1 つのワイルドカード証明書として、または対応するディレクトリにコピーされるエンドポイントごとの個別の証明書として、提供することができます。 どちらのオプションでも、証明書を必要とする **acs** や Key Vault などのエンドポイントにはワイルドカード証明書を使う必要があることに注意してください。 

| デプロイ フォルダー | 必要な証明書のサブジェクト名とサブジェクトの別名 (SAN) | スコープ (リージョンごと) | サブドメインの名前空間 |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| パブリック ポータル | portal.&lt;region>.&lt;fqdn> | ポータル | &lt;region>.&lt;fqdn> |
| 管理ポータル | adminportal.&lt;region>.&lt;fqdn> | ポータル | &lt;region>.&lt;fqdn> |
| Azure Resource Manager パブリック | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Azure Resource Manager 管理 | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(ワイルドカード SSL 証明書) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(ワイルドカード SSL 証明書) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(ワイルドカード SSL 証明書) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(ワイルドカード SSL 証明書) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(ワイルドカード SSL 証明書) |  内部 Keyvault |  adminvault.&lt;region>.&lt;fqdn> |

Azure AD デプロイ モードを使用して Azure Stack をデプロイする場合は、前の表に一覧表示されている証明書を要求するだけで済みます。 ただし、AD FS デプロイ モードを使用して Azure Stack をデプロイする場合は、次の表で説明されている証明書も要求する必要があります。

|デプロイ フォルダー|必要な証明書のサブジェクト名とサブジェクトの別名 (SAN)|スコープ (リージョンごと)|サブドメインの名前空間|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> このセクションに一覧表示されているすべての証明書のパスワードは同じである必要があります。 

## <a name="optional-paas-certificates"></a>オプションの PaaS 証明書
Azure Stack がデプロイおよび構成された後に追加の Azure Stack PaaS サービス (SQL、MySQL、および App Service) をデプロイする予定がある場合は、それらの PaaS サービスのエンドポイントに対応する追加の証明書を要求する必要があります。 

> [!IMPORTANT]
> App Service、SQL、および MySQL リソース プロバイダーに使用する証明書は、グローバル Azure Stack エンドポイントに使用されるものとルート証明機関が同じである必要があります。 

次の表では、SQL および MySQL アダプターと App Service に必要なエンドポイントと証明書について説明します。 これらの証明書を Azure Stack デプロイ フォルダーにコピーする必要はありません。 代わりに、追加のリソース プロバイダーをインストールするときにこれらの証明書を指定します。 

|スコープ (リージョンごと)|証明書|必要な証明書のサブジェクト名とサブジェクトの別名 (SAN)|サブドメインの名前空間|
|-----|-----|-----|-----|
|SQL、MySQL|SQL および MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(ワイルドカード SSL 証明書)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Web トラフィックの既定の SSL 証明書|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(マルチドメイン ワイルドカード SSL 証明書<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> 複数のワイルドカード サブジェクトの別名を持つ 1 つの証明書が必要です。 1 つの証明書での複数のワイルドカード SAN は、公的証明機関によってはサポートされない可能性があります。 

<sup>2</sup> これらの 3 つの証明書 (api.appservice.*&lt;region>.&lt;fqdn>*、ftp.appservice.*&lt;region>.&lt;fqdn>*、および sso.appservice.*&lt;region>.&lt;fqdn>*) の代わりに &#42;.appservice.*&lt;region>.&lt;fqdn>* ワイルドカード証明書を使用することはできません。 App Service では、これらのエンドポイントに個別の証明書を明示的に使用する必要があります。 

## <a name="learn-more"></a>詳細情報
[Azure Stack デプロイのための PKI 証明書を生成する](azure-stack-get-pki-certs.md)方法について理解を深めましょう。 

## <a name="next-steps"></a>次の手順
[ID の統合](azure-stack-integrate-identity.md)


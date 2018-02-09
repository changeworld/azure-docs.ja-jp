---
title: "Azure Stack 統合システムの Azure Stack 公開キー インフラストラクチャ証明書の要件 | Microsoft Docs"
description: "Azure Stack 統合システムの Azure Stack PKI 証明書のデプロイ要件について説明します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: c8dd2866e24faacfccff7f5f490710853f426345
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack 公開キー インフラストラクチャ証明書の要件
Azure Stack には、少数の Azure Stack サービスやテナント VM に割り当てられた外部からアクセス可能なパブリック IP アドレスを使用するパブリック インフラストラクチャ ネットワークが存在します。 Azure Stack のデプロイ中に、これらの Azure Stack パブリック インフラストラクチャ エンドポイントの適切な DNS 名を持つ PKI 証明書が必要です。 この記事では、次の項目に関する情報を提供します。

- Azure Stack をデプロイするために必要な証明書の種類
- これらの仕様に一致する証明書を取得するプロセス
- デプロイ中にこれらの証明書を準備、検証、および使用する方法

## <a name="certificate-requirements"></a>証明書の要件
次の一覧では、Azure Stack をデプロイするために必要な証明書の要件について説明します。 
- 証明書は、内部の証明機関または公的証明機関のどちらかから発行されている必要があります。 公的証明機関が使用されている場合は、Microsoft の信頼されたルート機関プログラムの一部として基本オペレーティング システム イメージに含まれている必要があります。 完全な一覧は、https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca で見つけることができます。 
- この証明書は、サブジェクトの別名 (SAN) フィールドにすべての名前空間を含む 1 つのワイルドカード証明書にすることができます。 あるいは、必要な場所にストレージや Key Vault などのエンドポイント用のワイルドカードを使用した個々の証明書を使用できます。 
- 証明書署名アルゴリズムを SHA1 にすることはできません。より強力にする必要があります。 
- Azure Stack のインストールには公開キーと秘密キーの両方が必要なため、証明書の形式は PFX である必要があります。 
- 証明書 pfx ファイルの "Key Usage" フィールドには、"Digital Signature" と "KeyEncipherment" の値が含まれている必要があります。
- デプロイの時点で、すべての証明書 pfx ファイルのパスワードが同じである必要があります。
- デプロイの失敗を回避するために、すべての証明書のサブジェクト名とサブジェクトの別名がこの記事で説明されている仕様に一致していることを確認してください。

> [!NOTE]
> 証明書の信頼チェーン内での中間証明機関の存在がサポートされています。 

## <a name="mandatory-certificates"></a>必須の証明書
このセクションの表では、Azure AD と AD FS Azure Stack の両方のデプロイに必要な Azure Stack パブリック エンドポイント PKI 証明書について説明します。 証明書の要件が領域のほか、使用される名前空間や、名前空間ごとに必要な証明書でグループ化されています。 この表ではまた、ソリューション プロバイダーがパブリック エンドポイントごとに異なる証明書をコピーするフォルダーについても説明します。 

各 Azure Stack パブリック インフラストラクチャ エンドポイントの適切な DNS 名を持つ証明書が必要です。 各エンドポイントの DNS 名は、*&lt;prefix>.&lt;region>.&lt;fqdn>* の形式で表されます。 

デプロイでは、[region] と [externalfqdn] の値が Azure Stack システム用に選択したリージョン名と外部ドメイン名に一致している必要があります。 例として、リージョン名が *Redmond* で、外部ドメイン名が *contoso.com* である場合、DNS 名は *&lt;prefix>.redmond.contoso.com* の形式になります。*&lt;prefix>* 値は、その証明書によってセキュリティ保護されるエンドポイントを指定するようにマイクロソフトによって事前に設計されています。 さらに、外部インフラストラクチャ エンドポイントの *&lt;prefix>* 値は、特定のエンドポイントを使用する Azure Stack サービスによって異なります。 

|デプロイ フォルダー|必要な証明書のサブジェクト名とサブジェクトの別名 (SAN)|スコープ (リージョンごと)|サブドメインの名前空間|
|-----|-----|-----|-----|
|パブリック ポータル|portal.*&lt;region>.&lt;fqdn>*|ポータル|*&lt;region>.&lt;fqdn>*|
|管理ポータル|adminportal.*&lt;region>.&lt;fqdn>*|ポータル|*&lt;region>.&lt;fqdn>*|
|Azure Resource Manager パブリック|management.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|Azure Resource Manager 管理|adminmanagement.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|次のサブジェクトの別名を持つ 1 つのマルチサブドメイン ワイルドカード証明書<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|ストレージ|blob.*&lt;region>.&lt;fqdn>*<br>table.*&lt;region>.&lt;fqdn>*<br>queue.*&lt;region>.&lt;fqdn>*|
|KeyVault|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>(ワイルドカード SSL 証明書)|Key Vault|vault.*&lt;region>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>(ワイルドカード SSL 証明書)|内部 Keyvault|adminvault.*&lt;region>.&lt;fqdn>*|
|
<sup>1</sup> ACS 証明書では、1 つの証明書に 3 つのワイルドカード SAN が必要です。 1 つの証明書での複数のワイルドカード SAN は、公的証明機関によってはサポートされない可能性があります。 

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
|App Service|Web トラフィックの既定の SSL 証明書|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(マルチドメイン ワイルドカード SSL 証明書<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL 証明書<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> 複数のワイルドカード サブジェクトの別名を持つ 1 つの証明書が必要です。 1 つの証明書での複数のワイルドカード SAN は、公的証明機関によってはサポートされない可能性があります。 

<sup>2</sup> これらの 3 つの証明書 (api.appservice.*&lt;region>.&lt;fqdn>*、ftp.appservice.*&lt;region>.&lt;fqdn>*、および sso.appservice.*&lt;region>.&lt;fqdn>*) の代わりに &#42;.appservice.*&lt;region>.&lt;fqdn>* ワイルドカード証明書を使用することはできません。 App Service では、これらのエンドポイントに個別の証明書を明示的に使用する必要があります。 

## <a name="learn-more"></a>詳細情報
[Azure Stack デプロイのための PKI 証明書を生成する](azure-stack-get-pki-certs.md)方法について理解を深めましょう。 

## <a name="next-steps"></a>次の手順
[ID の統合](azure-stack-integrate-identity.md)


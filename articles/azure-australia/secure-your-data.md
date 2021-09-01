---
title: Azure Australia におけるデータ セキュリティ
description: オーストラリアのリージョン内で、オーストラリア政府のポリシー、規制、法令に固有の要件を満たすための Azure の構成。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: d90dc7dd316e79407069a0c00f9d63659688a640
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "117029182"
---
# <a name="data-security-in-azure-australia"></a>Azure Australia におけるデータ セキュリティ

お客様のデータを保護するうえでの最大の原則は次のとおりです。

* 暗号化によってデータを保護する
* シークレットを管理する
* データ アクセスを制限する

## <a name="encrypting-your-data"></a>データを暗号化する

データの暗号化は、ディスク レベル (保存中)、データベース (保存中、転送中)、アプリケーション (転送中)、およびネットワーク上 (転送中) で適用できます。 Azure で暗号化を実現するには、次のようないくつかの方法があります。

|サービス/機能|説明|
|---|---|
|Storage Service Encryption|Azure Storage Service Encryption はストレージ アカウント レベルで有効になります。したがってブロック BLOB とページ BLOB は、Azure Storage への書き込み時に自動的に暗号化されます。 Azure Storage からデータを読み取ると、Storage サービスによって復号化されてから、返されます。 SSE を使用して、コードを変更したりアプリケーションにコードを追加したりすることなくデータをセキュリティで保護できます。|
|Azure Disk Encryption|Azure Virtual Machine に使用される OS ディスクとデータ ディスクを暗号化するには、Azure Disk Encryption を使用します。 Azure Key Vault との統合によって、ディスクの暗号化キーを制御し、効率よく管理することができます。|
|クライアント側のアプリケーション レベルの暗号化|クライアント側の暗号化は Java と .NET ストレージ クライアント ライブラリに組み込まれているので、Azure Key Vault API を使用して簡単に実装できます。 Azure Key Vault では、Azure Active Directory を使用して、Azure Key Vault 内のシークレットへのアクセス権を特定のユーザー用に取得します。|
|転送中の暗号化|Azure Australia への接続用に用意されている基本的な暗号化では、Transport Level Security (TLS) 1.2 プロトコルと X.509 証明書がサポートされます。 Azure Australia のデータセンター間におけるインフラストラクチャ ネットワーク接続には、Federal Information Processing Standard (FIPS) 140-2 Level 1 暗号アルゴリズムも使用されます。  Windows Server 2016、Windows 10、Windows Server 2012 R2、Windows 8.1、および Azure ファイル共有では、VM とファイル共有間の暗号化に SMB 3.0 を使用できます。 クライアント アプリケーションのストレージに転送する前のデータを暗号化したり、ストレージから転送された後のデータを復号化したりするには、クライアント側の暗号化を使用します。|
|IaaS VM|Azure Disk Encryption を使用します。 ディスクを Azure Storage にバックアップする際に使用する VHD ファイルは、Storage Service Encryption を有効にすることで暗号化します。ただし、その場合、暗号化されるのは、新しく書き込まれたデータだけです。 つまり、VM を作成してから、VHD ファイルを保持するストレージ アカウントで Storage Service Encryption を有効にすると、元の VHD ファイルではなく、変更のみが暗号化されます。|
|クライアント側の暗号化|この方法では、転送前にデータが暗号化され、保存データも暗号化されるため、最も安全といえます。 ただし、ストレージを使用してアプリケーションにコードを追加する必要があります。 ストレージを追加したくない場合は、転送中のデータに HTTPS を使用し、Storage Service Encryption を使用して保存データを暗号化する方法があります。 クライアント側の暗号化は、クライアントにかかる負荷が増加するため、スケーラビリティの計画でこの点を考慮する必要があります。特に、大量のデータを暗号化したり転送したりする場合に重要です。|
|

Azure における暗号化オプションの詳細については、[ストレージ セキュリティ ガイド](../storage/blobs/security-recommendations.md)に関するページを参照してください。

## <a name="protecting-data-by-managing-secrets"></a>シークレットを管理することでデータを保護する

クラウドにあるデータを保護するうえで、安全なキー管理は不可欠です。 キーの管理を省力化すると共に、クラウド アプリケーションやクラウド サービスでデータを暗号化する際に使用されるキーの管理に絶えず気を配るよう努めてください。

### <a name="managing-secrets"></a>シークレットを管理する

* ハードコーディングされた構成ファイルやスクリプト、ソース コード内からシークレットが漏えいするリスクを Key Vault を使用して最小限に抑えます。 Azure Key Vault は、FIPS 140-2 Level 2 認定のハードウェア セキュリティ モジュール (HSM) にキー (Azure Disk Encryption 用の暗号化キーなど) やシークレット (パスワードなど) を格納することで、それらを暗号化します。 さらに安心感を高めたい場合には、それらの HSM でキーのインポートや生成を行うことができます。
* アプリケーション コードやテンプレートに記述する必要があるのは、シークレットへの URI 参照のみです。つまり、実際のシークレットは、コード リポジトリ、構成リポジトリ、ソース コード リポジトリには存在しません。 これにより、GitHub での収集ボットのような、内外のリポジトリへのキー フィッシング攻撃を防ぐことができます。
* Key Vault 内で強力な Azure RBAC 制御を利用します。 信任されていたオペレーターが退職したり、社内の別の部署に異動になったりした場合は、そのオペレーターのシークレットへのアクセスを禁止する必要があります。  

詳細については、[Azure Key Vault](azure-key-vault.md) に関する記事を参照してください

## <a name="isolation-to-restrict-data-access"></a>分離によってデータ アクセスを制限する

分離の本質は、境界、区分、コンテナーを使用して、権限のあるユーザーやサービス、アプリケーションにデータへのアクセスを限定することです。 たとえば Microsoft Azure のようなマルチテナント型のクラウド プラットフォームでは、テナントどうしを分離することが、セキュリティ上の機構として不可欠になります。 論理上の分離によって、テナントが互いの運用に支障を及ぼすことを防ぐことができます。

#### <a name="per-customer-isolation"></a>顧客ごとの分離

Azure には、レイヤー 2 VLAN 分離、アクセス制御リスト、ロード バランサー、および IP フィルターによるネットワーク アクセス制御と分離が実装されています。

サブスクリプションやリソース グループ、仮想ネットワーク、サブネットによって、お客様はそのリソースの分離度をさらに高めることができます。

Microsoft Azure における分離の詳細については、「[Azure パブリック クラウドでの分離](../security/fundamentals/isolation-choices.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure VPN Gateway](vpn-gateway.md) に関する記事をご覧ください。
---
title: Azure Key Vault の概要 | Microsoft Docs
description: Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 07/17/2018
ms.author: barclayn
ms.openlocfilehash: b509bf9575c3fdeedd1673d4ffe1395d46e3d919
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051614"
---
# <a name="what-is-azure-key-vault"></a>Azure Key Vault とは

Azure Key Vault は、次の問題の解決に役立ちます
- Azure Key Vault を使用すると、トークン、パスワード、証明書、API キー、その他のシークレットを安全に格納し、それらへのアクセスを厳密に制御できます。
- Azure Key Vault は、キー管理ソリューションとしても使用できます。 Azure Key Vault により、データの暗号化に使用される暗号化キーの作成と制御が簡単になります。 
- Azure Key Vault は、Azure および内部の接続されているリソースで使用するためのパブリックおよびプライベートの Secure Sockets Layer/Transport Layer Security (SSL/TLS) 証明書を簡単にプロビジョニング、管理、展開することができるサービスでもあります。 
- シークレットとキーは、ソフトウェアまたは FIPS 140-2 レベル 2 検証済み HSM で保護できます

## <a name="why-use-azure-key-vault"></a>Azure Key Vault を使用する理由

### <a name="centralize-application-secrets"></a>アプリケーション シークレットの一元化

アプリケーション シークレットのストレージを Azure Key Vault に一元化することで、その配布を制御できます。 Key Vault によって、シークレットが誤って漏洩する可能性が大幅に小さくなります。 Key Vault を使用すると、アプリケーション開発者は、アプリケーションにセキュリティ情報を格納する必要がなくなります。 これにより、この情報をコードの一部にする必要がなくなります。 たとえば、必要に応じてデータベースに接続するアプリケーションがあるとします。 この場合、接続文字列をアプリ コードに格納する代わりに、Key Vault に安全に格納します。

アプリケーションのキーまたはシークレットが Azure Key Vault に格納された後、アプリケーションは、シークレットの特定のバージョンを取得できる URI を使用して、必要な情報に安全にアクセスできます。 これは、機密情報のいずれかを保護するカスタム コードを記述する必要なしに発生します。

### <a name="securely-store-secrets-and-keys"></a>シークレットとキーを安全に保存

シークレットとキーは、業界標準のアルゴリズム、キーの長さ、ハードウェア セキュリティ モジュール (HSM) を使用して、Azure によってセキュリティで保護されています。 使用される HSM では、Federal Information Processing Standards (FIPS) 140-2 Level 2 が検証されています。

キー コンテナーにアクセスする場合、呼び出し元 (ユーザーまたはアプリケーション) がアクセスする前に適切な認証と認可が必要になります。 認証では呼び出し元の ID を確認し、認可では呼び出し元が実行できる操作を決定します。

認証は Azure Active Directory を介して行われます。 認可は、ロールベースのアクセス制御 (RBAC) または Key Vault のアクセス ポリシーを使用して行うことができます。 RBAC は、コンテナーを管理するときに使用されます。キー コンテナーのアクセス ポリシーは、コンテナーに格納されているデータにアクセスするときに使用されます。

Azure Key Vault は、ソフトウェアまたはハードウェアの HSM で保護されます。 さらに追加の保証が必要な状況では、ハードウェア セキュリティ モジュール (HSM) 内でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 Microsoft では、Thales ハードウェア セキュリティ モジュールを使用しています。 Thales のツールを使用して、HSM からキーを Azure Key Vault に移動することができます。

最後に、Azure Key Vault は、Microsoft がデータを確認および抽出しないように設計されています。

### <a name="monitor-access-and-use"></a>アクセスおよび利用状況の監視

キー コンテナーをいくつか作成したら、キーとシークレットにアクセスする方法とタイミングを監視することをお勧めします。 そのためには、Key Vault のログを有効にします。 Azure Key Vault を構成して、次の操作を行うことができます。

- ストレージ アカウントへのアーカイブ。
- イベント ハブへのストリーム配信。
- Log Analytics へのログの送信。

ユーザーは、ログを管理できます。ユーザーは、アクセスを制限することによってログを保護することができ、不要になったログを削除することもできます。

### <a name="simplified-administration-of-application-secrets"></a>簡単なアプリケーション シークレットの管理

重要なデータを格納する場合は、いくつかの手順を実行する必要があります。 セキュリティ情報は、保護されていなければならず、ライフサイクルに従わなければならず、高可用性を備えていなければなりません。 Azure Key Vault では、次のことによってこの多くを簡単に実現できます。

- ハードウェア セキュリティ モジュールに関するインハウスの知識の必要性を排除する。
- 組織の使用量の急増に対応するために急なスケールアップを可能にする。
- リージョン内およびセカンダリ リージョンに Key Vault の内容をレプリケートする。 Key Vault によって高可用性が確保され、フェールオーバーをトリガーするための管理者の操作が不要になります。
- ポータル、Azure CLI、および PowerShell を使用した標準 Azure 管理オプションを提供する。
- パブリック CA から購入した証明書に関する特定のタスク (登録や更新など) を自動化する。

さらに、Azure Key Vault では、アプリケーション シークレットを分離することができます。 アプリケーションは、アクセスが許可されたコンテナーにのみアクセスし、特定の操作のみを実行するように制限することができます。 アプリケーションごとに Azure Key Vault を作成し、Key Vault に格納されているシークレットを特定のアプリケーションと開発者チームに制限することができます。

### <a name="integrate-with-other-azure-services"></a>他の Azure サービスとの統合

Key Vault は、Azure の安全なストアとして、[Azure Disk Encryption](../security/azure-security-disk-encryption.md)、SQL サーバーと Azure SQL の [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 機能、[Azure Web アプリ]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)のようなシナリオを単純化するために使用されてきました。 Key Vault 自体は、ストレージ アカウント、イベント ハブ、ログ分析と統合できます。

## <a name="next-steps"></a>次の手順

- [クイック スタート: CLI を使用した Azure Key Vault の作成](quick-create-cli.md)
- [キー コンテナーからシークレットを読み取るように Azure Web アプリケーションを構成する](tutorial-web-application-keyvault.md)

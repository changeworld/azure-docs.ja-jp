---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/21/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5fc36e327a9530105182f0a23b3ef22ab324e01c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803616"
---
- ファイアウォールと仮想ネットワークの構成があるストレージ アカウントへのアクセスを制限する必要がある
- Automation アカウント変数は、暗号化する必要がある
- Azure Cache for Redis は仮想ネットワーク内に存在しなければならない
- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Azure Spring Cloud でネットワークの挿入を使用する必要がある
- Cognitive Services アカウントでカスタマー マネージド キー (CMK) によるデータ暗号化を有効にする必要がある
- コンテナーの CPU とメモリの制限を強制する必要がある
- コンテナー イメージは信頼されたレジストリからのみデプロイする必要がある
- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- 特権エスカレーションを含むコンテナーは避ける必要がある
- 機密性の高いホストの名前空間を共有するコンテナーは避ける必要がある
- コンテナーは許可されたポートでのみリッスンする必要がある
- コンテナーで不変 (読み取り専用) のルート ファイル システムを適用する必要がある
- Key Vault キーには有効期限が必要である
- Key Vault シークレットには有効期限が必要である
- キー コンテナーで消去保護が有効になっている必要がある
- キー コンテナーで論理的な削除が有効になっている必要がある
- コンテナーで最小限の特権を持つ Linux 機能を適用する必要がある
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある
- コンテナーの AppArmor プロファイルのオーバーライドまたは無効化を制限する必要がある
- 特権コンテナーの使用を避ける
- コンテナーをルート ユーザーとして実行しない
- ストレージ アカウントへの安全な転送を有効にする必要がある
- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある
- Service Fabric クラスターは、クライアント認証に Azure Active Directory だけを使用する必要がある
- サービスは許可されたポートでのみリッスンする必要がある
- ストレージ アカウントのパブリック アクセスを禁止する必要がある
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある
- ストレージ アカウントは、仮想ネットワーク ルールを使用してネットワーク アクセスを制限する必要がある
- ホスト ネットワークとポートの使用を制限する必要がある
- ポッドの HostPath ボリューム マウントの使用を既知のリストに制限して、侵害されたコンテナーからのノード アクセスを制限する必要がある
- Azure Key Vault に保存されている証明書の有効期間は 12 か月以内にする必要がある
- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある
- Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある
- Azure Front Door Service サービスに対して Web Application Firewall (WAF) を有効にする必要がある


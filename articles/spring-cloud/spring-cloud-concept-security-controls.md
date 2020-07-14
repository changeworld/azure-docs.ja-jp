---
title: 概念 - Azure Spring Cloud Service のセキュリティ コントロール
description: Azure Spring Cloud Service に組み込まれているセキュリティ コントロールを使用します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 8d002fae52fec1fafb2ad8e63bd8e3b779a1537c
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984825"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud Service のセキュリティ コントロール
セキュリティ コントロールは、Azure Spring Cloud Service に組み込まれています。

セキュリティ コントロールとは、セキュリティの脆弱性の防止、検出、および対処を行う Azure サービスの能力に寄与する、そのサービスの品質または機能です。  各コントロールについて、 *[はい]* または *[いいえ]* を使用して、サービスに現在設定されているかどうかを示します。  サービスに適用されないコントロールには *[N/A]* を使用します。 

**データ保護のセキュリティ コントロール**

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|:-------------|:-------|:-------------------------------|:----------------------|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | ユーザーがアップロードしたソースと成果物、構成サーバーの設定、アプリの設定、永続的ストレージのデータは Azure Storage に格納されます。ここに格納されるコンテンツは自動的に暗号化されます。<br><br>構成サーバー キャッシュ、アップロードされたソースから構築されたランタイム バイナリ、およびアプリケーションの有効期間中のアプリケーション ログは、Azure マネージド ディスクに保存されます。ここに保存されるコンテンツは自動的に暗号化されます。<br><br>ユーザーがアップロードしたソースから構築されたコンテナー イメージは、Azure Container Registry に保存されます。ここに保存されるイメージ コンテンツは自動的に暗号化されます。 | [保存データに対する Azure Storage 暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Azure Managed Disks のサーバー側暗号化](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Azure Container Registry へのコンテナー イメージの保存](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| 一時的な暗号化 | はい | ユーザー アプリのパブリック エンドポイントには、既定で受信トラフィックに HTTPS が使用されます。 |  |
| API 呼び出しの暗号化 | はい | Azure Spring Cloud サービスを構成するための管理呼び出しは、HTTPS 経由の Azure Resource Manager 呼び出しを介して行われます。 | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**ネットワーク アクセスのセキュリティ コントロール**

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|:-------------|:-------|:-------------------------------|:----------------------|
| サービス タグ | はい | **AzureSpringCloud** サービス タグを使用して、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)または [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) で送信ネットワーク アクセスの制御を定義し、Azure Spring Cloud アプリケーションへのトラフィックを許可します。<br><br>*注:* 現時点では、2020/07/07 より後に作成された新しい Azure Spring Cloud サービス インスタンスでのみ、**AzureSpringCloud** サービス タグがサポートされています。 | [サービス タグ](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |

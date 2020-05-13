---
title: 概念 - Azure Spring Cloud Service のセキュリティ コントロール
description: Azure Spring Cloud Service に組み込まれているセキュリティ コントロールを使用します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594986"
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


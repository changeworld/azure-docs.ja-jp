---
title: Azure Data Share のセキュリティの概要
description: Azure Data Share のセキュリティの概要
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578095"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share のセキュリティの概要

この記事では、Azure Data Share サービスのセキュリティの概要について説明します。

## <a name="security-overview"></a>セキュリティの概要

Azure Data Share では、保存時と転送中のデータを保護するために Azure によって提供される基盤のセキュリティが利用されます。 データは保存時に暗号化され、基になるデータ ストアでサポートされます。 データは転送中も TLS 1.2 を使用して暗号化されます。 データ共有に関するメタデータも、保存時と転送中に暗号化されます。 Azure Data Share には、共有されている顧客データの内容は保存されません。

Azure Data Share では、データ共有に使用されているデータ ストアにアクセスするために、マネージド ID (以前の MSI) が利用されます。 データ プロバイダーとデータ コンシューマーの間で資格情報が交換されることはありません。 マネージド ID の詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) に関する記事を参照してください。 データを共有するために必要なロールとアクセス許可の詳細については、[ロールとアクセス許可](concepts-roles-permissions.md)に関する記事を参照してください。

承認されたユーザーによってアクセスされることを保証するために、Azure Data Share へのアクセスの制御を Data Share のリソース レベルで設定することができます。 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>ファイアウォールが有効になっているデータ ストアとの間でデータを共有する
ファイアウォールがオンになっているストレージ アカウントとの間でデータを共有するには、ストレージ アカウントで **[信頼された Microsoft サービスを許可する]** を有効にする必要があります。 詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)」を参照してください。


## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。

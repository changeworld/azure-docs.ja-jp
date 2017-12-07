---
title: "Azure Files のスケーラビリティとパフォーマンスのターゲット | Microsoft Docs"
description: "Azure Files のスケーラビリティとパフォーマンスのターゲットについて、容量、要求レート、送受信の帯域幅の制限を含めて、説明します。"
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 381e96a0a777415b916e4093fe55aa0d355782a1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Files のスケーラビリティおよびパフォーマンスのターゲット
[Azure Files](storage-files-introduction.md) はクラウドで、業界標準の SMB プロトコルを介してアクセスできる、完全に管理されたファイル共有を提供します。 この記事では、Azure Files と Azure File Sync (プレビュー) のスケーラビリティとパフォーマンスのターゲットについて説明します。

ここに掲載したスケーラビリティとパフォーマンスのターゲットはハイエンドのターゲットですが、デプロイの中でその他の変数の影響を受ける可能性があります。 たとえば、ファイルのスループットは、Azure Files サービスをホストするサーバーだけではなく、使用可能なネットワーク帯域幅によっても制限されます。 Azure Files のパフォーマンスとスケーラビリティが要件を満たしているかどうかを判断するには、実際の使用パターンでテストすることを強くお勧めします。 弊社では、順次これらの制限値を上げることにも取り組んでいます。 ページ下端のコメント欄や、[Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) から、どの制限値が上がることを期待しているかについて、フィードバックをお寄せください。

## <a name="azure-storage-account-scale-targets"></a>Azure ストレージ アカウントのスケール ターゲット
Azure ファイル共有の親リソースは、Azure ストレージ アカウントです。 ストレージ アカウントは、Azure Files を含む複数のストレージ サービスがデータを格納するために使用できる、Azure 内のストレージのプールを表しています。 ストレージ アカウントにデータを格納する他のサービスには、Azure Blob Storage、Azure Queue Storage、Azure Table Storage があります。 次のターゲットは、ストレージ アカウントにデータを格納するすべてのストレージ サービスに適用されます。

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 使用しているストレージ アカウント内での、他のストレージ サービスによるストレージ アカウントの使用状況は、Azure ファイル共有に影響を与えます。 たとえば、Azure Blob Storage でストレージ アカウントの最大容量に達すると、Azure ファイル共有では最大共有サイズを下回っていても、Azure ファイル共有で新しいファイルを作成できなくなります。

## <a name="azure-files-scale-targets"></a>Azure Files のスケール ターゲット
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync のスケール ターゲット
Azure File Sync では、できる限り使用量無制限を目指して設計しましたが、それを実現できない場合もあります。 弊社のテストの境界線と、どのターゲットが実際のハード制限かを次の表に示します。

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>関連項目
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)
- [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../common/storage-scalability-targets.md)
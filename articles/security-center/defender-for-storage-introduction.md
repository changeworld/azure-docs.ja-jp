---
title: Azure Defender for Storage - 利点と機能
description: Azure Defender for Storage の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9b2855f82927b6f1707fd748f097dd357818ac4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341976"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Azure Defender for Storage の概要

**Azure Defender for Storage** では、Azure Storage アカウント上の潜在的に有害なアクティビティが検出されます。 BLOB コンテナー、ファイル共有、またはデータ レイクのいずれに格納されているデータでも保護できます。

この保護層により、セキュリティの専門家で "*なくても*" 脅威に対処し、セキュリティ監視システムを管理できます。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**Azure Defender for Storage** は、[価格ページ](security-center-pricing.md)に記載されているように課金されます|
|保護されるストレージの種類:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov、その他の Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Azure Defender for Storage で提供されるアラートの種類

セキュリティ アラートは、次のような場合にトリガーされます。

- **疑わしいアクティビティ** - たとえば、ストレージ アカウントが Tor のアクティブな出口ノードとして知られている IP アドレスから正常にアクセスされました
- **異常な動作** - たとえば、ストレージ アカウントへのアクセス パターンの変化
- **マルウェアがアップロードされた可能性** - ハッシュ評価分析で、アップロードされたファイルにマルウェアが含まれていることが示されています

アラートには、それらをトリガーするインシデントの詳細と、脅威の調査や修復方法に関する推奨事項が含まれています。

> [!TIP]
> [こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)の手順に従って、ストレージのアラートをシミュレートできます。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>マルウェアのハッシュ評価分析とは

アップロードされたファイルが疑わしいかどうかを判断するため、Azure Defender for Storage では、[Microsoft 脅威インテリジェンス](https://go.microsoft.com/fwlink/?linkid=2128684)でサポートされているハッシュ評価分析が使用されます。 脅威の防止ツールでは、アップロードされたファイルがスキャンされるのではなく、ストレージ ログが調べられ、新しくアップロードされたファイルのハッシュと、既知のウイルス、トロイの木馬、スパイウェア、ランサムウェアのハッシュが比較されます。 

ファイルにマルウェアが含まれている疑いがある場合、Security Center ではアラートが表示されます。また、必要に応じて、疑わしいファイルの削除の承認を求めるメールをストレージの所有者に送信できます。 ハッシュ評価分析によってマルウェアが含まれることが示されているファイルの自動削除を設定するには、["マルウェアがストレージ アカウントにアップロードされた可能性" を含むアラートでトリガーするワークフローの自動化](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)をデプロイします。

> [!NOTE]
> Security Center の脅威防止機能を有効にするには、Azure Defender を、適用可能なワークロードを含むサブスクリプションで有効にする必要があります。
>
> **Azure Defender for Storage** は、サブスクリプション レベルまたはリソース レベルで有効にできます。



## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for Storage について説明しました。

関連資料については、次の記事をご覧ください。 

- アラートは、Security Center によって生成されたか、別のセキュリティ製品の Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。
- [Advanced Defender for Storage を有効にする方法](../storage/common/azure-defender-storage-configure.md)
- [Azure Defender for Storage アラートの一覧](alerts-reference.md#alerts-azurestorage)
- [Microsoft の脅威インテリジェンスの機能](https://go.microsoft.com/fwlink/?linkid=2128684)
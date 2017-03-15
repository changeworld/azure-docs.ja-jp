---
title: "Azure Data Lake Store のリージョン間の移行に関するガイダンス | Microsoft Docs"
description: "Azure Data Lake Store のリージョン間の移行に関するガイダンス"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 3c83a76c589178d5a2a01ed4edd208e42fb2e83e
ms.openlocfilehash: 785f97a28c060f88a0e0f3a5aa9cf6555ed2b5a4
ms.lasthandoff: 02/22/2017


---
# <a name="guidance-to-migrate-azure-data-lake-store-across-regions"></a>リージョン間で Azure Data Lake Store を移行するためのガイダンス

Azure Data Lake Store が新しいリージョンで利用できるようになった時点で、新しいリージョンを利用するために&1; 回限りの移行を行うことができます。  ここでは、移行の計画時と実行時に考慮すべき点に関するガイダンスを示します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **2 つの異なるリージョンの Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、[Azure Data Lake Store の使用開始](data-lake-store-get-started-portal.md)に関するページを参照してください。
* **Azure Data Factory**。  詳細については、[Azure Data Factory の概要](../data-factory/data-factory-introduction.md)に関するページを参照してください。


## <a name="guidance-for-migration"></a>移行に関するガイダンス

最初に、Azure Data Lake Store でのデータの書き込み、読み取り、または処理を行うアプリケーションに適した移行戦略を明確にすることをお勧めします。 戦略を選択する際は、アプリケーションの可用性要件 (ダウンタイムなど) を考慮する必要があります。たとえば、最も簡単な方法は、データの "リフトアンドシフト" です。  つまり、すべてのデータを新しいリージョンにコピーしながら古いリージョンでアプリケーションを一時停止します。  コピー プロセスが完了したら、新しいリージョンでアプリケーションを再開し、古い Azure Data Lake Store アカウントを削除することができます。  ただし、この移行中にはダウンタイムが発生します。

また、ダウンタイムを短縮するために、すぐに新しいリージョンで新しいデータの取り込みを開始し、必要最小限のデータが揃ったらすぐに新しいリージョンでアプリケーションを実行することもできます。  バックグラウンドで、古いデータを古い Azure Data Lake Store アカウントから新しいリージョンの新しい Azure Data Lake Store アカウントにコピーすることができます。  これにより、短いダウンタイムで新しいリージョンに切り替えることができます。  古いデータがすべてコピーされたら、古い ADLS アカウントを削除できます。

ほかにも、移行を計画する際に考慮すべき重要な事項があります。

* **データの量** - データの量 (GB、ファイルやフォルダーの数など) は、移動に必要な時間とリソースに影響します。

* **Azure Data Lake Store アカウント名** - 新しいリージョンの新しいアカウント名はグローバルに一意である必要があります。たとえば、contosoeastus2.azuredatalakestore.net が米国東部 2 の古いストア アカウントの名前である場合、北ヨーロッパのストア アカウントには contosonortheu.azuredatalakestore.net という名前を付けることができます。

* **ツールの選択** - Azure Data Lake Store のファイルをコピーする場合は、[Azure Data Factory のコピー アクティビティ](../data-factory/data-factory-azure-datalake-connector.md)を使用することをお勧めします。   Azure Data Factory では、パフォーマンスと信頼性に優れたデータ移動がサポートされています。  Azure Data Factory によってコピーされるのは、フォルダー階層とファイルの内容のみであることに注意してください。 適用したアクセス制御リスト (ACL) はすべて、新しいアカウントに手動で適用する必要があります。  [Azure Data Factory のパフォーマンス チューニングに関するガイダンス](../data-factory/data-factory-copy-activity-performance.md)は、最良のシナリオのパフォーマンス目標の参考になります。  より短い期間でデータをコピーしたい場合は、追加のクラウド データ移動単位の使用が必要になることがあります。  ADLCopy などのその他のツールではリージョン間のデータのコピーがサポートされていないことに注意してください。  

* **[帯域幅の料金](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)** - Azure リージョンからデータが転送されるため、料金が発生します。

* **データの ACL** - ファイルとフォルダーに ACL を適用して、新しいリージョンのデータをセキュリティで保護します。  これに関するガイダンスについては、[こちら](data-lake-store-secure-data.md)を参照してください。  この移行を ACL の更新と調整の機会として考えることをお勧めします。  ただし、同様の設定を使用する場合は、任意のファイルに適用されている ACL をポータル、[PowerShell コマンドレット](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.datalakestore/v3.1.0/get-azurermdatalakestoreitempermission)、または SDK を使用して表示できます。  

* **分析サービスの場所** - 最良のパフォーマンスを得るには、Data Lake Analytics や HDInsight などの分析サービスをデータと同じリージョンに配置する必要があります。  

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)


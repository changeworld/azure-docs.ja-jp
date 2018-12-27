---
title: ビジネス継続性 - Microsoft Genomics | Microsoft Docs
titleSuffix: Azure
description: この概要では、Microsoft Genomics が提供するビジネス継続性とディザスター リカバリーの機能について説明します。 Azure リージョンの停止など、データ損失の原因となる可能性のある破壊的なイベントから復旧するためのオプションについて説明します。
keywords: ビジネス継続性, ディザスター リカバリー
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: be678648ab93fcbdfd0a0baa1b01dcb273060ce2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729454"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Microsoft Genomics によるビジネス継続性の概要
この概要では、Microsoft Genomics が提供するビジネス継続性とディザスター リカバリーの機能について説明します。 Azure リージョンの停止など、データ損失の原因となる可能性のある破壊的なイベントから復旧するためのオプションについて説明します。 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>ビジネス継続性をサポートする Microsoft Genomics の機能 
まれに、Azure データ センターが停止し、数分から数時間にわたり業務が中断する可能性があります。 停止が発生すると、データ センターで現在実行されているすべてのジョブが失敗し、Microsoft Genomics サービスはセカンダリ リージョンにジョブを自動的に再送信しません。 

* オプションの 1 つは、データ センターの停止が終了し、データ センターがオンラインに戻るのを待つことです。 停止が短い場合、Microsoft Genomics サービスは失敗したジョブを自動的に検出し、ワークフローは自動的に再起動されます。

* もう 1 つのオプションは、別のデータ リージョンにワークフローを事前に送信しておくことです。 Microsoft Genomics は複数の [Azure リージョン](https://azure.microsoft.com/regions/services/)にインスタンスを展開し、各リージョンに固有のインスタンスは独立しています。 Microsoft Genomics インスタンスの 1 つでリージョンのローカルな障害が発生した場合、Microsoft Genomics のインスタンスを実行している他のリージョンはジョブの処理を続けます。 このような代わりのリージョンへの転送は、ユーザーが制御でき、いつでも利用できます。


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Microsoft Genomics のワークフローを別のリージョンに手動でフェールオーバーする
リージョン規模でデータ センターの停止が発生した場合、個々のデータの主権とビジネス継続性の要件に基づいて、Microsoft Genomics のワークフローをセカンダリ リージョンに送信することができます。 Microsoft Genomics のワークフローを手動でフェールオーバーするには、異なるリージョン固有の  Genomics アカウントを使用し、適切なリージョン固有の Genomics アカウントとストレージ アカウントの資格情報を使用してジョブを送信します。

具体的には、次のようにする必要があります。
* Azure Portal を使用して、セカンダリ リージョンに Genomics アカウントを作成します。 
* 入力データをセカンダリ リージョンのストレージ アカウントに移行し、セカンダリ リージョンで出力フォルダーを設定します。
* セカンダリ リージョンにワークフローを送信します。

元のリージョンが復元しても、Microsoft Genomics サービスはセカンダリ リージョンから元のリージョンにデータを移行して戻すことはありません。 ユーザーは、セカンダリ リージョンから元のリージョンに入力ファイルと出力ファイルを移動することができます。  データの移動を選択した場合、その処理は Genomics サービスの外部で行われ、データ移動に関連するすべての請求はユーザーの責任になります。 

### <a name="preparing-for-a-possible-region-specific-outage"></a>可能性のあるリージョン固有の停止への準備
データ センター停止時の高速復旧について懸念がある場合は、いくつかの手順を実行することで、セカンダリ リージョンに Microsoft Genomics のワークフローを手動で再送信するための時間を短縮できます。

* 適切なセカンダリ リージョンを特定し、そのリージョンに Genomics アカウントを前もって作成しておきます
* プライマリ リージョンとセカンダリ リージョンでデータを複製し、セカンダリ リージョンでデータをすぐに利用できるようにします。 これは、手動で行うことも、Azure Storage で利用できる [geo 冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy)を使用して行うこともできます。 

## <a name="next-steps"></a>次の手順
この記事では、Microsoft Genomics サービスを使用するときのビジネス継続性とディザスター リカバリーのオプションについて説明しました。 Azure での一般的なビジネス継続性とディザスター リカバリーについては、「[Azure の回復性技術ガイダンス](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)」をご覧ください。 
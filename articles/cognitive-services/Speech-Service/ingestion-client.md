---
title: インジェスト クライアント - 音声サービス
titleSuffix: Azure Cognitive Services
description: この記事では、顧客が音声ファイルを音声サービスに簡単かつ迅速にプッシュできるようにする、GitHub でリリースされたツールについて説明します
services: cognitive-services
author: panosperiorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 069c0b587fd3ff69ca8e883850d44b84ab12486f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743577"
---
# <a name="ingestion-client-for-the-speech-service"></a>音声サービス用インジェスト クライアント

インジェスト クライアントは [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/ingestion) にリリースされているツールです。顧客は、これを使用して、開発作業をほとんどまたはまったく行わずに、音声サービスを通して音声ファイルの文字起こしをすばやく行うことができます。 これは、専用の [Azure ストレージ](https://azure.microsoft.com/product-categories/storage/) アカウントをカスタム [Azure Functions](https://azure.microsoft.com/services/functions/) に接続し、サーバーレス方式で [REST API](rest-speech-to-text.md) または [SDK](speech-sdk.md) を使用して、サービスに文字起こし要求を渡すことによって機能します。  

## <a name="architecture"></a>Architecture

このツールは、事前に開発投資を行うことなく、文字起こしの品質を把握したいと考えている顧客に役立ちます。 このツールでは、いくつかのリソースが接続され、専用の [Azure Storage コンテナー](https://azure.microsoft.com/product-categories/storage/)に配置されている音声ファイルの文字起こしが実行されます。

内部的には、このツールでは、V3.0 Batch API または SDK を使用し、スケールアップ、再試行、フェールオーバーを処理するためのベスト プラクティスに従っています。 次の図は、リソースと接続について説明しています。

:::image type="content" source="media/ingestion-client/architecture-1.png" alt-text="インジェスト クライアントアーキテクチャ。":::

[インジェスト クライアントの概要のガイド](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md)では、ツールをセットアップして使用する方法について説明しています。

> [!IMPORTANT]
> 価格は、選択した Azure Function SKU と操作モード (バッチまたはリアルタイム) によって異なります。 このツールでは、大量のボリュームを処理するための Premium Azure Function SKU が既定で作成されます。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

Microsoft [SPEECH SDK](speech-sdk.md) と [Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) の両方を使用して、音声テキストを取得できます。 ガイドで説明されているように、この決定は全体のコストに影響します。 

> [!TIP]
> ツールおよび結果として得られるソリューションを運用環境で使用して、大量のオーディオを処理することができます。

## <a name="tool-customization"></a>ツールのカスタマイズ

このツールは、顧客に結果をすばやく表示するように構築されています。 好みの SKU と設定に合わせて、ツールをカスタマイズできます。 SKU は [Azure portal](https://portal.azure.com) から編集でき、[コード自体は GitHub で入手できます](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)。

> [!NOTE]
> コストをより簡単に把握して追跡するために、同じ専用リソース グループにリソースを作成することをお勧めします。

## <a name="next-steps"></a>次のステップ

* [音声サービス試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [Speech SDK の詳細を確認する](./speech-sdk.md)
* [Speech CLI ツールの詳細を確認する](./spx-overview.md)

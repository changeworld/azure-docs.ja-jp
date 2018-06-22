---
title: Azure Data Lake Analytics での U-SQL Cognitive 機能の使用
description: U-SQL の Cognitive 機能のインテリジェンスを使用する方法
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: ab40d466d7b60dd09b8953012c80d0e84f4ac471
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802070"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>U-SQL の Cognitive 機能を使い始める

## <a name="overview"></a>概要
U-SQL の Cognitive 機能により、開発者はビッグ データ プログラムにインテリジェンスを配置できます。 

次の認識機能を使用できます。
* イメージング: 顔を検出する[例](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* イメージング: 感情を検出する[例](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* イメージング: オブジェクトを検出する (タグ付け) [例](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* イメージング: OCR (光学式文字認識) [例](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* テキスト: キー フレーズ抽出および感情分析[例](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>U-SQL での Cognitive 拡張機能の登録
開始する前に、「[U-SQL での Cognitive 拡張機能の登録](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/cognitive-capabilities-in-u-sql#registeringExtensions)」の記事の手順に従い、U-SQL で Cognitive 拡張機能を登録します。

## <a name="next-steps"></a>次の手順
* [U-SQL/Cognitive のサンプル](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](data-lake-analytics-use-window-functions.md)

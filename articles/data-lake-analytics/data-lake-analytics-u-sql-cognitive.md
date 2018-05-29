---
title: Azure Data Lake Analytics で U-SQL Cognitive 機能の使用 |Microsoft Docs
description: U-SQL の Cognitive 機能のインテリジェンスを使用する方法
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: cd06e1ae56efdfdcfcd4fec5b2c17ee843d9e9dd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311116"
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>チュートリアル: U-SQL の Cognitive 機能を使い始める

## <a name="overview"></a>概要
U-SQL の Cognitive 機能により、開発者はビッグ データ プログラムにインテリジェンスを配置できます。 

次の認識機能を使用できます。
* イメージング: 顔を検出する
* イメージング: 感情を検出する
* イメージング: オブジェクトを検出する (タグ付け)
* イメージング: OCR (光学式文字認識)
* テキスト: キー フレーズ抽出
* テキスト: 感情分析

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>U-SQL スクリプトで Cognitive を使用する方法

全体的なプロセスは簡単です。

* `REFERENCE ASSEMBLY` ステートメントを使用して U-SQL スクリプトの認識機能を有効にします
* Cognitive UDO で入力行セットに対して `PROCESS` を使用して出力行セットを生成します。

### <a name="detecting-objects-in-images"></a>イメージ内のオブジェクトを検出する

次の例では、イメージ内のオブジェクトを検出するために認識機能を使用する方法を説明します。

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
その他の例については、「**次のステップ**」セクションの「**U-SQL/Cognitive Samples**」(U-SQL/Cognitive のサンプル) を参照してください。

## <a name="next-steps"></a>次の手順
* [U-SQL/Cognitive のサンプル](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](data-lake-analytics-use-window-functions.md)

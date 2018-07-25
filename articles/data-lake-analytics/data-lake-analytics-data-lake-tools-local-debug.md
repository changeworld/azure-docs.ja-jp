---
title: ローカルで Azure Data Lake Analytics コードをデバッグする | Microsoft Docs
description: Azure Data Lake Tools for Visual Studio を使用してローカル ワークステーション上で U-SQL ジョブをデバッグする方法について説明します。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890814"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>ローカルで Azure Data Lake Analytics コードをデバッグする

Azure Data Lake Tools for Visual Studio を使用すると、Azure Data Lake サービスで実行するときと同じように、ワークステーションで Azure Data Lake Analytics コードを実行およびデバッグすることができます。

[ローカル コンピューターで U-SQL スクリプトを実行する方法](data-lake-analytics-data-lake-tools-local-run.md)について説明します。

## <a name="debug-scripts-and-c-assemblies-locally"></a>スクリプトと C# アセンブリをローカルでデバッグする

Azure Data Lake Analytics サービスに提出し、登録しなくても C# アセンブリをデバッグできます。 分離コード ファイルと参照 C# プロジェクトの両方にブレークポイントを設定できます。

### <a name="to-debug-local-code-in-code-behind-file"></a>分離コード ファイルのローカル コードをデバッグするには

1. 分離コード ファイルにブレークポイントを設定します。
2. F5 キーを押して、スクリプトをローカルでデバッグします。

> [!NOTE]
   > 次の手順は、Visual Studio 2015 でのみ機能します。 以前の Visual Studio では、pdb ファイルを手動で追加する必要があります。  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>参照先の C# プロジェクトのローカル コードをデバッグするには

1. C# アセンブリ プロジェクトを作成してビルドし、出力 dll を生成します。
2. U-SQL ステートメントを使用して dll を登録します。

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. C# コードにブレークポイントを設定します。
4. F5 キーを押し、C# dll をローカルで参照してスクリプトをデバッグします。


## <a name="next-steps"></a>次の手順

- より複雑なクエリを表示する場合は、[Azure Data Lake Analytics を使用した Web サイトのログの分析](data-lake-analytics-analyze-weblogs.md)に関するページをご覧ください。
- ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
- 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。

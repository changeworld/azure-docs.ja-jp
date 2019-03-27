---
title: Azure Data Factory の Mapping Data Flow を作成する
description: Azure Data Factory の Mapping Data Flow を作成する
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: b706e229bed48c821d5ca772450df320fd7e0b7f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271398"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory のデータ フローを作成する

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

ADF の Mapping Data Flow は、コーディングなしでデータを大規模に変換する方法を提供します。 データ フロー デザイナーで一連の変換を構築することで、データ変換ジョブを設計できます。 任意の数のソースの変換から始め、その後にデータ変換手順を実行します。 次に、データ フローをシンクで完了して、結果を宛先に書き込みます。

Azure portal で新しい V2 データ ファクトリを作成することから始めます。 新しいファクトリを作成したら、[作成者と監視] タイルをクリックして、Data Factory UI を起動します。

![Data Flow オプション](media/data-flow/v2dataflowportal.png "データフローの作成")

Data Factory UI が表示されたら、サンプル データ フローを使用できます。 サンプルは、ADF テンプレート ギャラリーから入手できます。 ADF で、"テンプレートからパイプライン" を作成し、テンプレート ギャラリーから [データ フロー] カテゴリを選択します。

![Data Flow オプション](media/data-flow/template.png "データフローの作成")

Azure Blob ストレージ アカウント情報の入力を求められます。

![Data Flow オプション](media/data-flow/template2.png "データ フローの作成 2")

[これらのサンプルで使用されるデータはこちらで見つけることができます](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 サンプル データをダウンロードし、Azure Blob ストレージ アカウントにファイルを格納して、サンプルを実行できるようにします。

ADF UI で、[+] ボタン (リソースの作成) を使用して、データソースを作成します。

![Data Flow オプション](media/data-flow/newresource.png "新しいリソース")


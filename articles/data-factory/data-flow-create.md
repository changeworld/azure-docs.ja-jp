---
title: マッピング データ フローを作成する
description: Azure Data Factory のマッピング データ フローを作成する方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 83b40121de72dc45582ded94580154f5124817b0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061012"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory のデータ フローを作成する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

ADF の Mapping Data Flow は、コーディングなしでデータを大規模に変換する方法を提供します。 データ フロー デザイナーで一連の変換を構築することで、データ変換ジョブを設計できます。 任意の数のソースの変換から始め、その後にデータ変換手順を実行します。 次に、データ フローをシンクで完了して、結果を宛先に書き込みます。

Azure portal で新しい V2 データ ファクトリを作成することから始めます。 新しいファクトリを作成した後、[Open Azure Data Factory Studio (Azure Data Factory Studio を開く)] タイルで [開く] を選択し、Data Factory UI を起動します。

:::image type="content" source="media/data-flow/v2portal.png" alt-text="スクリーンショットに、[新しいデータ ファクトリ] ペインが示され、[バージョン] に [V2] が選択されています。":::

Data Factory UI が表示されたら、サンプル データ フローを使用できます。 サンプルは、ADF テンプレート ギャラリーから入手できます。 ADF で、ホームページの [Discover more (さらに検出)] セクションで [パイプラインのテンプレート] タイルを選択し、テンプレート ギャラリーから [データ フロー] カテゴリを選択します。

:::image type="content" source="media/data-flow/template.png" alt-text="スクリーンショットに、選択されたデータ フローを使用したデータ変換がある [Data Flow] タブが示されています。":::

Azure Blob ストレージ アカウント情報の入力を求められます。

:::image type="content" source="media/data-flow/template2.png" alt-text="スクリーンショットに、データ フロー ペインを使用したデータの変換が示されています。ここで、ユーザー入力を入力できます。":::

[これらのサンプルで使用されるデータはこちらで見つけることができます](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 サンプル データをダウンロードし、Azure Blob ストレージ アカウントにファイルを格納して、サンプルを実行できるようにします。

## <a name="create-new-data-flow"></a>新しいデータ フローを作成する

ADF UI で、[+] ボタン (リソースの作成) を使用して、データ フローを作成します。

:::image type="content" source="media/data-flow/newresource.png" alt-text="スクリーンショットに、[ファクトリ リソース] メニューから選択された Data Flow が示されています。":::

## <a name="next-steps"></a>次のステップ

[ソース変換](data-flow-source.md)を使用してデータ変換の構築を開始します。

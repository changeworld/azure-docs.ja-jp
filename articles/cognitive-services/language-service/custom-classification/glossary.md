---
title: カスタム テキスト分類で使用される定義
titleSuffix: Azure Cognitive Services
description: カスタム テキスト分類で使用される定義について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 0699f4c32a62be3b07fd795357302f71ffcce8c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091503"
---
# <a name="terms-and-definitions-used-in-custom-text-classification"></a>カスタム テキスト分類で使用される用語と定義 

カスタム テキスト分類で使用される定義について説明します。 

## <a name="project"></a>プロジェクト

プロジェクトは、データに基づいてカスタム AI モデルを構築するための作業領域です。 プロジェクトにアクセスできるのは、自分と、使用されている Azure リソースへの共同作成者アクセス権を持つユーザーのみです。
カスタム テキスト分類プロジェクトを作成するための前提条件として、[リソースをストレージ アカウントに接続](how-to/create-project.md)する必要があります。
プロジェクト作成フローの一部として、データセットをアップロードした BLOB コンテナーにそれを接続する必要があります。 プロジェクトには、コンテナー内にあるすべての `.txt` ファイルが自動的に組み込まれます。 同じデータセットで構築された複数のモデルをプロジェクト内に含めることができます。 詳細については、[サービス制限](service-limits.md)に関する記事を参照してください。

プロジェクト内では、次の操作を実行できます。

* [データにタグ付けする](./how-to/tag-data.md): モデルをトレーニングするときにファイルの分類方法が学習されるように、データセットの各ファイルにそれぞれのクラスでタグ付けするプロセス。
* [モデルを構築してトレーニングする](./how-to/train-model.md): プロジェクトの中心となる手順。 この手順では、モデルはタグ付けされたデータから学習を開始します。 
* [モデルの評価の詳細を表示する](./how-to/view-model-evaluation.md): モデルのパフォーマンスをレビューして、改善の余地があるかどうか、または結果に満足できるかどうかを判断します。
* [モデルを改善する (省略可能)](./how-to/improve-model.md): モデルの問題を特定し、パフォーマンスを向上させます。
* [モデルをデプロイする](quickstart.md?pivots=language-studio#deploy-your-model): モデルを使用できるようにします。 
* [モデルをテストする](quickstart.md?pivots=language-studio#test-your-model): モデルをテストし、パフォーマンスを確認します。

### <a name="project-types"></a>プロジェクトの種類

カスタム テキスト分類では、次の 2 種類のプロジェクトがサポートされています。

* **単一ラベル分類** - データセットの各ファイルに対して 1 つのクラスのみを割り当てることができます。 たとえば、映画の脚本の場合、ファイルは `Action`、`Thriller`、または `Romance` のいずれかのみになります。

* **複数ラベル分類** - データセットの各ファイルに対して **複数の** クラスを割り当てることができます。 たとえば、映画の脚本の場合、ファイルは `Action`、または `Action` と `Thriller` または `Romance` の場合があります。

## <a name="model"></a>モデル

モデルは、特定のタスク (ここでは、カスタム テキスト分類) を実行するためにトレーニングされるオブジェクトです。 モデルは、タグ付けされたデータを提供することによってトレーニングされ、後でテキストを分類するために使用できます。 モデルのパフォーマンスに問題がなければ、デプロイでき、それによって、[使用できる](https://aka.ms/ct-runtime-swagger)ようになります。

## <a name="class"></a>クラス

クラスは、テキストの全体的な分類を示すために使用されるユーザー定義カテゴリです。 データには、割り当てられたクラスでタグ付けしてから、トレーニングを行うためにモデルに渡します。 

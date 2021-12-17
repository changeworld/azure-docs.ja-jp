---
title: カスタム テキスト分類の制限
titleSuffix: Azure Cognitive Services
description: カスタム テキスト分類を使用するときのデータとレートの制限について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, references_regions, ignite-fall-2021
ms.openlocfilehash: 06db585531cb3a73b291f2c8d45de18a3d298fb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484088"
---
# <a name="custom-text-classification-limits"></a>カスタム テキスト分類の制限

この記事では、カスタム テキスト分類を使用するときのデータとレートの制限について説明します

## <a name="file-limits"></a>ファイルの制限

* カスタム テキスト分類には `.txt` ファイルのみを使用することができます。 データが別の形式の場合は、[CLUtils 解析コマンド](https://aka.ms/CognitiveServicesLanguageUtilities)を使用してドキュメントを解読し、テキストを抽出できます。

* コンテナーにアップロードするすべてのファイルにはデータが含まれている必要があり、空のファイルをトレーニングに使用することはできません。

* すべてのファイルは、コンテナーのルートで利用できる必要があります。

* [トレーニング データセット](how-to/train-model.md#data-splits)には、10 以上 1,000,000 以下のファイルを含める必要があります。

## <a name="api-limits"></a>API の制限

**作成 API**

* 1 分あたり最大 10 件の POST 要求と 100 件の GET 要求を送信できます。

**分析 API**

* 1 分あたり最大 20 件の GET 要求または POST 要求を送信できます。

* 1 要求あたりのファイルの最大サイズは 125, 000 文字です。 ファイルの合計サイズが 125, 000 文字を超えない限り、最大 25 個のファイルを送信できます。

> [!NOTE]
> 制限を超えるファイルを送信する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 この処理には、[CLUtils の chunk コマンド](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md)を使用することができます。

## <a name="azure-resource-limits"></a>Azure リソースの制限

* リソースごとに接続できるストレージ アカウントは 1 つだけです。 この処理は元に戻すことができません。 ストレージ アカウントをリソースに接続した場合、後で切断することはできません。

* リソースあたり最大 500 のプロジェクトを使用することができます。

* プロジェクト名は、カスタム固有表現認識 (NER) 機能とカスタム テキスト分類機能の両方で、同じリソース内で一意である必要があります。

## <a name="regional-availability"></a>リージョン別の提供状況 

カスタム テキスト分類は、一部の Azure リージョンでのみ使用することができます。 [Azure リソース](how-to/create-project.md)を作成するときは、次のリージョンのいずれかにデプロイする必要があります。
* **米国西部 2**
* "**西ヨーロッパ**"

## <a name="project-limits"></a>プロジェクトの制限

* ストレージ コンテナーは、プロジェクトごとに 1 つしか接続できません。 この処理は元に戻すことができません。コンテナーをプロジェクトに接続した場合、後で切断することはできません。

* タグ ファイルは、プロジェクトごとに 1 つしか作成できません。 後でタグ ファイルを変更することはできません。プロジェクト内のタグのみを更新できます。

* 作成後にプロジェクト名を変更することはできません。

* プロジェクトには、10 個以上のファイルが必要であり、ファイル数は最大 1,000,000 個です。

* プロジェクトあたり最大 10 個のトレーニング済みモデルを使用することができます。

* モデル名は、同じプロジェクト内で一意である必要があります。

* 作成後にモデル名を変更することはできません。

* 一度にトレーニングできるモデルは、プロジェクトごとに 1 つだけです。

## <a name="classes-limits"></a>クラスの制限

* プロジェクトには、少なくとも 2 つのクラスが必要です。 <!-- The maximum is 200 classes. -->

* クラスごとに 200 個のタグ付きインスタンスを使用することをお勧めします。また、クラスごとに少なくとも 10 個のタグ付きインスタンスを持つ必要があります。

## <a name="naming-limits"></a>名前付けの制限

| 属性 | 制限 |
|--|--|
| プロジェクト名 |  使用できるのは文字 `(a-z, A-Z)` と数字 `(0-9)` のみで、スペースを含めることはできません。 |
| モデル名 |  使用できるのは文字 `(a-z, A-Z)`、数字 `(0-9)`、記号 `@ # _ . , ^ \ [ ]` のみです。 |
| エンティティ名| 使用できるのは文字 `(a-z, A-Z)`、数字 `(0-9)`、記号 `@ # _ . , ^ \ [ ]` のみです。 |
| ファイル名 | 使用できるのは文字 `(a-z, A-Z)` と数字 `(0-9)` のみで、スペースを含めることはできません。 |

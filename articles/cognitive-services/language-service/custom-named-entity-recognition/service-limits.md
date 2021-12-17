---
title: カスタム固有表現認識 (NER) サービスの制限
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) を使う場合のデータとサービスの制限について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: 255c0f5f85f1f7b385fed82c36a799004f03ed0b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488218"
---
# <a name="custom-named-entity-recognition-ner-service-limits"></a>カスタム固有表現認識 (NER) サービスの制限

この記事では、カスタム NER を使う場合のデータとサービスの制限について説明します。

## <a name="file-limits"></a>ファイルの制限

* 使用できるのは、`.txt` ファイルのみです。 データが別の形式の場合は、[CLUtils 解析コマンド](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md)を使ってドキュメントを開き、テキストを抽出できます。

* コンテナーにアップロードされたすべてのファイルにデータが含まれている必要があります。 トレーニングには空のファイルを使うことはできません。

* すべてのファイルは、コンテナーのルートで利用できる必要があります。

* ファイルの最大許容長は 128,000 文字で、約 28,000 ワードまたは 56 ページです。

* [トレーニング データセット](how-to/train-model.md#data-split)には、10 以上 100,000 以下のファイルを含める必要があります。


## <a name="apis-limits"></a>API の制限

* Authoring API を使う場合、1 分あたりの POST 要求は最大 10 回、GET 要求は最大 100 回となります。

* Analyze API を使う場合、1 分あたりの GET 要求または POST 要求は最大 20 回となります。

* 要求ごとの最大ファイル サイズは 125,000 文字です。 全体が 125,000 文字を超えない限り、最大 25 ファイルを送信できます。

> [!NOTE]
> 制限を超えるファイルを送信する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 この処理には、[CLUtils の Chunk コマンド](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md)を使えます。

## <a name="azure-resource-limits"></a>Azure リソースの制限

* リソースごとに接続できるストレージ アカウントは 1 つだけです。 この処理は元に戻すことができません。 ストレージ アカウントをリソースに接続した場合、後でリンクを解除することはできません。

* リソースあたり最大 500 のプロジェクトを使用することができます。

* プロジェクト名は、カスタム NER と[カスタム テキスト分類](../custom-classification/overview.md)の両方で、同一リソース内で一意である必要があります。

## <a name="regional-availability"></a>リージョン別の提供状況 

カスタム テキスト分類は、一部の Azure リージョンでのみ使用することができます。 [Azure リソース](how-to/create-project.md)を作成するときは、次のリージョンのいずれかにデプロイする必要があります。
* **米国西部 2**
* "**西ヨーロッパ**"
    
## <a name="project-limits"></a>プロジェクトの制限

* ストレージ コンテナーは、プロジェクトごとに 1 つしか接続できません。 この処理は元に戻すことができません。 コンテナーをプロジェクトに接続した場合、後で切断することはできません。

* プロジェクトごとに 1 つの[タグ ファイル](how-to/tag-data.md)のみ含めることができます。 後で別のタグ ファイルに変更することはできません。 プロジェクト内のタグのみを更新できます。

* 作成後にプロジェクト名を変更することはできません。

* プロジェクトには、10 以上のタグ付きファイルが必要で、ファイル数は最大 100,000 です。

* プロジェクトあたり最大 10 個のトレーニング済みモデルを使用することができます。

* モデル名は、同じプロジェクト内で一意である必要があります。

* 作成後にモデル名を変更することはできません。

* 一度にトレーニングできるモデルは、プロジェクトごとに 1 つだけです。

## <a name="entity-limits"></a>エンティティの制限

* タグ付けされたエンティティは 10 文字を超えないようにすることをお勧めしますが、許可される最大文字数は 100 文字です。

* プロジェクトには少なくとも 1 つのエンティティ型が必要で、エンティティ型は最大 200 です。

* エンティティごとに 200 のタグ付きインスタンスを使うことをお勧めします。また、エンティティごとに少なくとも 10 のタグ付きインスタンスを持つ必要があります。

## <a name="naming-limits"></a>名前付けの制限

| 属性 | 制限 |
|--|--|
| プロジェクト名 |  使用できるのは文字 `(a-z, A-Z)` と数字 `(0-9)` のみで、スペースを含めることはできません。 |
| モデル名 |  使用できるのは文字 `(a-z, A-Z)` と数字 `(0-9)` のみで、スペースを含めることはできません。 |
| エンティティ名| 使えるのは文字 `(a-z, A-Z)`、数字 `(0-9)` と記号 `@ # _ . , ^ \ [ ]` のみです |
| ファイル名 | 使用できるのは文字 `(a-z, A-Z)` と数字 `(0-9)` のみで、スペースを含めることはできません。 |

## <a name="next-steps"></a>次のステップ

[カスタム NER の概要](../overview.md)

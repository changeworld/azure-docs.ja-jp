---
title: Cognitive Services 用のカスタマー マネージド キー
titleSuffix: Cognitive Services
description: Azure portal を使用して、Azure Key Vault でカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1d161c82c087fd86a3774f0d121330260b1574e4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366096"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Cognitive Services 用に Azure Key Vault でカスタマー マネージド キーを構成する

Cognitive Services 用に Azure Key Vault でカスタマー マネージド キーを有効にするプロセスは、製品ごとに異なります。 サービス固有の手順については、以下のリンクを使用してください。

## <a name="vision"></a>視覚

* [Custom Vision による保存データの暗号化](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [Face サービスによる保存データの暗号化](../Face/face-encryption-of-data-at-rest.md)
* [Form Recognizer による保存データの暗号化](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>Language

* [Language Understanding サービスでの保存データの暗号化](../LUIS/luis-encryption-of-data-at-rest.md)
* [QnA Maker での保存データの暗号化](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Translator による保存データの暗号化](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>決定

* [Content Moderator による保存データの暗号化](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Personalizer による保存データの暗号化](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault とは](../../key-vault/general/overview.md)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
---
title: Personalizer でマルチスロットを使用する方法
description: Personalizer でマルチスロットを使用して、サービスによって提供されるコンテンツのレコメンデーションを向上させる方法について説明します。
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: ea1f244b43becd65b3bc85f2e40dc2c029ccbf43
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829271"
---
# <a name="get-started-with-multi-slot-for-azure-personalizer"></a>Azure Personalizer のマルチスロットの概要

マルチスロット パーソナル化 (プレビュー) を使用すると、複数のアクション (製品やコンテンツなど) がユーザーに示される Web レイアウト、カルーセル、リストで、コンテンツを対象にすることができます。 Personalizer のマルチスロット API を使用すると、Personalizer 内の AI モデルで、ユーザー インターフェイス内の配置を考慮し、それから学習して、特定の動作をもたらすユーザー コンテキストや製品を学習できます。 たとえば、特定の製品やコンテンツでは、ページのメインの強調表示にするより、サイド バーやフッターにする方が、クリック数が増えることを、Personalizer で学習できます。 

このガイドでは、Personalizer のマルチスロット API を使用する方法について説明します。

::: zone pivot="programming-language-csharp"

[!INCLUDE [Try multi-slot with C#](./includes/quickstart-multislot-csharp.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [Try multi-slot with Node.js](./includes/quickstart-multislot-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Try multi-slot with Python](./includes/quickstart-multislot-python.md)]

::: zone-end

## <a name="next-steps"></a>次の手順

* [マルチスロットについてさらに学習する](concept-multi-slot-personalization.md)
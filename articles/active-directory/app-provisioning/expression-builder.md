---
title: Application Provisioning in Azure Active Directory で式ビルダーを使用する方法を理解する
description: Application Provisioning in Azure Active Directory で式ビルダーを使用する方法を理解します。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/02/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ed5623f2b7d4585a7ffe417dbfa0943bf37ebf21
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991472"
---
# <a name="understand-how-expression-builder-in-application-provisioning-works"></a>Application Provisioning で式ビルダーを使用する方法を理解する

[式](functions-for-customizing-application-data.md)を使用して[属性をマッピングする](./customize-application-attributes.md)ことができます。 以前は、これらの式を手動で作成して、式の入力欄に入力する必要がありました。 式ビルダーは、式の作成に役立つツールです。

:::image type="content" source="media/expression-builder/expression-builder.png" alt-text="関数を選ぶ前の、式ビルダーの最初のページ。" lightbox="media/expression-builder/expression-builder.png":::

式を作る方法については、「[Azure Active Directory で属性マッピングの式を記述するためのリファレンス](functions-for-customizing-application-data.md)」をご覧ください。 

## <a name="finding-the-expression-builder"></a>式ビルダーを見つける

アプリケーションのプロビジョニングでは、式を使用して属性をマッピングします。 属性マッピングのページで **[詳細オプションの表示]** 、 **[式ビルダー]** を順に選んで式ビルダーに移動します。

:::image type="content" source="media/expression-builder/accessing-expression-builder.png" alt-text="詳細設定を選択していることを示すチェックボックスと、式ビルダーへのリンクが表示されています" lightbox="media/expression-builder/accessing-expression-builder.png":::

## <a name="using-expression-builder"></a>式ビルダーを使用する

式ビルダーを使用するには、機能と属性を選び、必要ならばサフィックスを入力します。 次に、 **[式の追加]** をクリックして、コード欄に式を追加します。 利用できる機能とその使用方法の詳細は「[Azure Active Directory で属性マッピングの式を記述するためのリファレンス](functions-for-customizing-application-data.md)」をご覧ください。

ユーザーを検索するか値を入力して、 **[式のテスト]** をクリックすることで、式をテストします。 式のテストの出力が **[式の出力を表示]** 欄に表示されます。

式に満足したら、これを属性マッピングに移動します。 これをコピーし、作成している属性マッピングの式の欄に貼り付けます。

## <a name="known-limitations"></a>既知の制限事項
* 式ビルダーでは、拡張属性を選択できません。 ただし、属性マッピングの式では拡張属性を使用できます。 

## <a name="next-steps"></a>次のステップ

「[Azure Active Directory で属性マッピングの式を記述するためのリファレンス](functions-for-customizing-application-data.md)」
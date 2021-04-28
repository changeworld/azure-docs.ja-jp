---
author: baanders
description: Azure Digital Twins を使用してテナント間の制限に対するコード ソリューションを記述するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589378"
---
次の例は、`DefaultAzureCredential` オプションで `InteractiveBrowserTenantId` のテナント ID 値を設定する方法を示しています。

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="DefaultAzureCredentialOptions メソッドを示しているコードのスクリーンショット。InteractiveBrowserTenantId の値は、サンプルのテナント ID 値に設定されています。":::

Visual Studio と Visual Studio Code での認証のためにテナントを設定する場合にも、同様のオプションが用意されています。 使用可能なオプションの詳細については、[DefaultAzureCredentialOptions のドキュメント](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)を参照してください。
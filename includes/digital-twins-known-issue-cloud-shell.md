---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Cloud Shell 認証に関する既知の問題を示します
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92324613"
---
>[!NOTE]
>Cloud Shell には現在、 *https://shell.azure.com* から実行した場合に `az dt route` 、`az dt model` 、`az dt twin` の各コマンド グループに影響する **既知の問題** があります。
>
>解決するには、次のいずれかを行うことができます。
> * コマンドを実行する前に Cloud Shell で `az login` を実行する。
> * Azure portal の [Cloud Shell] ペインを開き、そこから Cloud Shell 作業を完了する。
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="&quot;Cloud Shell&quot; アイコンが強調表示されている Azure portal のビューと、ポータル ウィンドウの下部に表示されている Cloud Shell":::
> * Cloud Shell ではなく、[ローカル CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用する。
>
>この問題の詳細については、「 [*トラブルシューティング: Azure Digital Twins の既知の問題*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)」を参照してください。
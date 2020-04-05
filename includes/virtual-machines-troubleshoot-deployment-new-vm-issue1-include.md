---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181173"
---
## <a name="issue-custom-image-provisioning-errors"></a>問題: カスタム イメージ - プロビジョニング エラー
プロビジョニング エラーは、一般化された VM イメージを特殊化された VM イメージとしてアップロードまたはキャプチャした場合、あるいは特殊化された VM イメージを一般化された VM イメージとしてアップロードまたはキャプチャした場合に発生します。 前者の場合はプロビジョニング タイムアウト エラーが発生し、後者の場合はプロビジョニングが失敗します。 エラーを発生させずにカスタム イメージをデプロイするには、キャプチャ プロセス中にイメージの種類が変更されないようにする必要があります。

次の表に、一般化されたイメージと特殊化されたイメージの考えられる組み合わせ、発生するエラーの種類、エラーを解決するために行う必要があることを示します。


---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021324"
---
## <a name="create-base-resources"></a>基本のリソースの作成

メッセージ ルーティングを構成するには、IoT ハブ、ストレージ アカウント、および Service Bus キューを事前に作成する必要があります。 これらのリソースは、このチュートリアルのパート 1 にある 4 つの記事 (Azure portal、Azure Resource Manager テンプレート、Azure CLI、または Azure PowerShell) のいずれかを使用して作成できます。

すべてのリソースに同じリソース グループと場所を使います。 最後に、リソース グループを削除することによって、すべてのリソースを一度に削除できます。

次に、以下のセクションで実行する手順の概要を示します。 

1. [リソース グループ](../articles/azure-resource-manager/management/overview.md)を作成します。

2. S1 レベルに IoT ハブを作成します。 コンシューマー グループを IoT ハブに追加します。 コンシューマー グループは、データを取得するときに Azure Stream Analytics によって使われます。

   > [!NOTE]
   > このチュートリアルを完了するには、有料レベルで IoT ハブを使用する必要があります。 無料レベルで設定できるのは 1 つのエンドポイントのみです。このチュートリアルでは複数のエンドポイントが必要です。
   > 

3. Standard_LRS レプリケーションで Standard V1 ストレージ アカウントを作成します。

4. Service Bus の名前空間とキューを作成します。

5. ハブにメッセージを送信するシミュレートされたデバイスのデバイス ID を作成します。 テスト フェーズ用にキーを保存します。 (Resource Manager テンプレートを作成する場合、これはテンプレートをデプロイした後に行います)。
---
title: クイック スタート:ソリューションを構成する
description: このクイックスタートでは、Azure Security Center for IoT を使用してエンド ツー エンドの IoT ソリューションを構成する方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e6ab713715cacc799d2b980c2bce2a2a15b76887
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505456"
---
# <a name="quickstart-configure-your-iot-solution"></a>クイック スタート:IoT ソリューションを構成する

この記事では、Azure Security Center for IoT を使用して IoT セキュリティ ソリューションの初期構成を実行する方法について説明します。

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

Azure Security Center for IoT では、Azure ベースの IoT ソリューションに対して包括的なエンド ツー エンドのセキュリティが提供されます。

Azure Security Center for IoT を使用すると、1 つのダッシュボードで IoT ソリューション全体を監視し、Azure 内のすべての IoT デバイス、IoT プラットフォーム、およびバックエンド リソースを明確に把握することができます。

Azure Security Center for IoT を IoT ハブ上で有効にすると、IoT ハブに接続されていて IoT ソリューションに関連している他の Azure サービスが自動的に識別されます。

自動でリレーションシップを検出できるだけでなく、他のどの Azure リソース グループを IoT ソリューションの一部としてタグ付けするかを選択することもできます。

選択に応じて、サブスクリプション全体、リソース グループ、または単一のリソースを追加できます。

すべてのリソースのリレーションシップを定義すると、Azure Security Center for IoT によって、これらのリソースに対するセキュリティのレコメンデーションとアラートが Azure Security Center を通じて提供されるようになります。

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT ソリューションへの Azure リソースの追加

IoT ソリューションに新しいリソースを追加するには、次の操作を行います。

1. Azure portal で **[IoT Hub]** を開きます。
1. 左側のメニューの **[セキュリティ]** セクションから **[設定]** を選択して開き、 **[監視対象リソース]** を選択します。
1. **[編集]** を選択し、自分の IoT ソリューションに属している監視対象リソースを選択します。
1. **[追加]** をクリックします。

お疲れさまでした。 IoT ソリューションに新しいリソース グループが追加されました。

これで、Azure Security Center for IoT によって、新たに追加されたリソース グループが監視され、関連するセキュリティのレコメンデーションとアラートが IoT ソリューションの一部として明確に示されるようになりました。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、セキュリティ モジュールの作成方法を学習してください。

> [!div class="nextstepaction"]
> [セキュリティ モジュールを作成する](quickstart-create-security-twin.md)

---
title: 'クイックスタート: Azure リソースを IoT ソリューションに追加する'
description: このクイックスタートでは、Azure Defender for IoT を使用してエンド ツー エンドの IoT ソリューションを構成する方法について説明します。
ms.topic: quickstart
ms.date: 09/13/2021
ms.openlocfilehash: e66accdc1ba671941c13433eeca9b3e9c541781b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634826"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>クイック スタート:Azure Defender for IoT ソリューションを構成する

この記事では、初めて Defender for IoT を使用して IoT セキュリティ ソリューションを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- なし

## <a name="what-is-defender-for-iot"></a>Defender for IoT とは

Defender for IoT は、Azure ベースの IoT ソリューションに対して包括的なエンド ツー エンドのセキュリティを提供します。

Defender for IoT を使用すると、1 つのダッシュボードで IoT ソリューション全体を監視し、Azure 内のすべての IoT デバイス、IoT プラットフォーム、およびバックエンド リソースを明確に把握することができます。

IoT Hub で Defender for IoT を有効にすると、Defender for IoT によって他の Azure サービスが自動的に特定され、IoT ソリューションと連携する関連サービスに接続されます。

また、IoT ソリューションの一部である他の Azure リソース グループを選択することもできます。

選択に応じて、サブスクリプション全体、リソース グループ、または単一のリソースを追加できます。

すべてのリソース関係を定義すると、Defender for IoT によって、これらのリソースに対するセキュリティの推奨事項とアラートが Defender を通じて提供されるようになります。

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT ソリューションへの Azure リソースの追加

**IoT ソリューションに新しいリソースを追加するには**:

1. Azure portal で、「**IoT Hub**」を検索して選択します。

1. [セキュリティ] セクションで、 **[設定]**  >  **[Monitored Resources]\(監視対象リソース\)** を選択します。

1. **[編集]** を選択し、自分の IoT ソリューションに属している監視対象リソースを選択します。

1. **[追加]** を選択します。

これで、新しいリソース グループが IoT ソリューションに追加されます。

これで、Defender for IoT によって、新たに追加されたリソース グループ、攻撃対象領域に関連するセキュリティの推奨事項、およびアラートが IoT ソリューションの一部として監視されるようになりました。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、Defender-IoT-micro-agent の作成方法を学習してください。

> [!div class="nextstepaction"]
> [Defender-IoT-micro-agent の作成](quickstart-create-security-twin.md)

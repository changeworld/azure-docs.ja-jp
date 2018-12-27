---
title: IoT Edge のモジュール マーケットプレースに関してよくあるご質問 | Microsoft Docs
description: IoT Edge のモジュール マーケットプレースに関してよくあるご質問。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807466"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>IoT Edge のモジュール マーケットプレースに関してよくあるご質問


## <a name="what-is-the-edge-module-marketplace"></a>Edge のモジュール マーケットプレースとは何ですか?


IoT Edge のモジュール マーケットプレースとは、Azure Marketplace から*見つけることができる*、事前に構築された*認定* Edge モジュールのリストです。

![IoT Edge のモジュール](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Edge のモジュールはどこにありますか? 


[Azure portal の Marketplace](https://ms.portal.azure.com/) (認証済みエクスペリエンス) の [モノのインターネット] カテゴリの下で、"IoT Edge Module" というタグで示されています。

さらに、[Azure Marketplace Web サイト](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (匿名エクスペリエンス) の [モノのインターネット] カテゴリの下で、"IoT Edge Module" というタグで示されています。

## <a name="is-it-open-to-partners"></a>パートナーに開放されていますか?


まだありません。 現時点でマーケットプレースの IoT Edge セクションで公開されているのは、Microsoft が作成したモジュールだけです。 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>パートナーが IoT Edge モジュールを公開すべきなのはなぜですか?


-   このチャネルをマーケットに追加してソリューションを紹介することで、製品の認知度を高めるためです。

-   さらに多くの潜在顧客を獲得するためです。 Azure Marketplace の一部として、誰が自社のソリューションに興味を持っているかを詳しく知ることができます。

-   収益化のためのより多くの機能をいち早く利用するためです。

## <a name="what-is-the-onboarding-process"></a>配布準備プロセスとは何ですか?


まだ公開されていませんが、Azure Marketplace を通じて配布準備プロセスが行われます。 詳しいガイドラインが「[Azure Marketplace と AppSource の公開ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)」にあります。 コンテナーについては、トランザクションの登録リストの種類を参照してください。 

パートナーはまず Azure Marketplace の公開元になる必要があります。 その後、[Cloud パートナー ポータル](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md)から Edge モジュールの配布準備ができます。

## <a name="are-there-any-monetization-capabilities"></a>収益化機能はありますか?


今のところ、そのまま使用できる既定のものはありません。 第一目標は、*無料*や*ライセンス持ち込み*の Edge モジュールを用意したマーケットプレースを開くことです。 使用量課金モデルなどの収益化機能は今後追加していく予定です。

## <a name="what-is-bring-your-own-license-byol"></a>ライセンス持ち込み (BYOL) とは


[Microsoft Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)には、次のような公式な定義があります。

- *お客様はサービス/データをアクセスまたは使用する権利を Azure Marketplace の外部で取得し、Azure Marketplace で提供されるサービス/データの使用に対して Azure Marketplace 料金を課金されません。*

ソフトウェアをライセンス化して料金を徴収するかどうかは、パートナーに任されています。

## <a name="can-partners-publish-a-freemium-module"></a>パートナーは “フリーミアム” モジュールを公開できますか?


フリーミアム モジュール (無料で入手できるもののいくつかの制限があるモジュール) は、その他の公開物と見なされます。

## <a name="is-intellectual-property-protected"></a>知的所有権は保護されますか?


Edge モジュールは、Docker と互換性があるコンテナーです。 配布コンテナーにパッケージした知的財産 (IP) を保護するかどうかはパートナーに任されています。

## <a name="where-will-the-modules-be-hosted"></a>モジュールはどこでホストされますか?


IoT Edge モジュールは、Docker Hub のように匿名でクエリ可能な Microsoft 所有のコンテナー レジストリでホストされます。

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Azure Marketplace と Azure IoT ツールの統合計画とは何ですか?

Azure Marketplace と Azure IoT ツールの間の統合はいっそう緊密になる予定です。 たとえば、IoT Hub ポータルまたは Visual Studio Code から IoT Edge モジュール マーケットプレースに直接ブラウジングできるようにしたいと考えています。

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>コンテナーはどのオペレーティング システムまたはアーキテクチャをサポートする必要がありますか?

IoT Edge モジュールは、実稼働環境の IoT Edge と同じ OS /アーキテクチャのマトリックスをサポートしている必要があります。 このリストについては、「[Azure IoT Edge サポート](https://docs.microsoft.com/azure/iot-edge/support)」に掲載されています。 たとえば、現時点で、モジュールは Linux x64 や Linux ARM32 をサポートする必要があります。

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>留意すべき認定資格の制約が他にも何かありますか?

一連の厳密な認定資格の制約を策定しているところです。 基本原則は、次のとおりです。

-   量より質を重視。

-   IoT Edge 固有のコンテナー (無作為ではない)。

-   運用環境グレード。

-   1 クリックでのデプロイ (少なくとも一連の既定の構成値が指定される)。

## <a name="any-other-questions"></a>その他の質問について


[Azure IoT Edge オンボード](mailto:azureiotedgeonboarding@service.microsoft.com)にお問い合わせください。

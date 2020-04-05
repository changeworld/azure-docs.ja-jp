---
title: Azure IoT Edge モジュール オファー発行の概要 | Azure Marketplace
description: Azure Marketplace に IoT Edge モジュール オファーを発行するプロセスの概要です。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: dsindona
ms.openlocfilehash: 6b44d9fd0bdd6e4b41013373472b5882a7ca1434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286593"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge モジュール オファー発行の概要

<table> <tr> <td>このセクションでは、新しい Azure IoT Edge モジュール オファーを Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> に公開する方法について説明します。 IoT Edge モジュールは、IoT Edge デバイスで実行するように作成された、Docker と互換性のあるコンテナーです。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure サービスまたはカスタム ソリューションのコードを含めることができます。 </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>公開プロセス

IoT Edge モジュール オファーを発行する手順の概要は次のとおりです。

1. オファーを作成する<br> オファーに関する詳細情報を指定します。 この情報には、オファーの説明、マーケティング資料、サポート情報、資産の仕様などが含まれます。

2. ビジネス資産と技術資産を作成する<br> 関連付けられたソリューション (Azure Container Registry でホストされている IoT Edge モジュール イメージ) に対するビジネス資産 (法務文書とマーケティング資料) と技術資産を作成します。

3. SKU を作成する<br> オファーに関連付けられた SKU を作成します。 発行を計画しているイメージごとに固有の SKU が必要です。

4. オファーを認定して発行する <br>オファーとテクノロジ資産が完成したら、オファーを送信することができます。 この送信によって発行プロセスが開始します。 このプロセスの間に、ソリューションがテストされ、検証され、認定されて、Azure Marketplace での "運用が開始" されます。

## <a name="parts-of-an-offer"></a>オファーの主要部分

以下の記事では、IoT Edge モジュール オファーの重要な部分について説明されています。

- [前提条件](./cpp-prerequisites.md) <br>この記事では、IoT Edge モジュール オファーを作成または発行する前の技術的およびビジネス的な要件の一覧を示します。
- [IoT Edge モジュールの技術資産の準備](./cpp-create-technical-assets.md) <br>この記事では、IoT Edge モジュールの技術資産を準備する方法について説明します。 IoT Edge モジュールを Azure Marketplace に発行するには、これらの資産が必要な技術的条件をすべて満たしている必要があります。
- [IoT Edge モジュール プランの作成](./cpp-create-offer.md) <br>この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com)を使用して新しい IoT Edge モジュール オファー エントリを作成するために必要な手順を示します。
- [IoT Edge モジュール プランの公開](./cpp-publish-offer.md)<br> この記事では、Azure Marketplace に発行するためにオファーを送信する方法について説明します。

## <a name="next-steps"></a>次のステップ

Microsoft Azure Marketplace に IoT Edge モジュールを発行するための[技術的およびビジネス的な要件](./cpp-prerequisites.md)を確認してください。

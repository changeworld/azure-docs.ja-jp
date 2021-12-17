---
title: Azure IoT Central からのデータ エクスポートのトラブルシューティング | Microsoft Docs
description: IoT Central でのデータ エクスポートに関する問題のトラブルシューティング
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 0fd283c1f3740e3e06f7a41cc66874596159e8f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092795"
---
# <a name="troubleshoot-issues-with-data-exports-from-your-azure-iot-central-application"></a>Azure IoT Central アプリケーションからのデータ エクスポートに関する問題のトラブルシューティング

このドキュメントでは、IoT Central アプリケーションのデータが意図された宛先に到達しない、または正しい形式で受信されない理由を確認するのに役立ちます。

## <a name="managed-identity-issues"></a>マネージド ID の問題

エクスポート先への接続を承認するために、マネージド ID を使用しています。 データは、エクスポート先に到着していません。

エクスポート先を構成または有効にする前に、次の手順を完了していることを確認してください。

- アプリケーションのマネージド ID を有効にする。
- マネージド ID のアクセス許可を構成する。
- 任意の仮想ネットワーク、プライベート エンドポイント、およびファイアウォール ポリシーを構成する。

詳細については、[データのエクスポート](howto-export-data.md?tabs=managed-identity)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

さらにサポートが必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートにお問い合わせください。 または、[Azure サポート チケット](https://portal.azure.com/#create/Microsoft.Support)を送信することもできます。

詳細については、「[Azure IoT のサポートとヘルプのオプション](../../iot-fundamentals/iot-support-help.md)」を参照してください。

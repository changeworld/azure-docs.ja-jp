---
title: Azure Video Analyzer の SDK とリソース
description: Azure Video Analyzer の SDK についての詳細情報
author: bennage
ms.author: christb
ms.topic: reference
ms.date: 05/14/2021
ms.openlocfilehash: 25e94b06fbc45d16ef198e2d77a653a407fcf1c9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954450"
---
# <a name="azure-video-analyzer-sdks"></a>Azure Video Analyzer の SDK

Azure Video Analyzer には、2 つのグループの SDK が含まれています。 管理 SDK は Azure リソースの管理に使用され、クライアント SDK はエッジ モジュールとの相互作用に使用されます。

## <a name="management-sdks"></a>管理 SDK

管理 SDK を使用して、Azure Resource Manager によって公開されるリソースと相互作用できます。 Video Analyzer アカウントの作成、エッジ モジュールのプロビジョニング トークンの生成、ビデオのアクセス ポリシーの管理などを行うことができます。 SDK は、基盤となる [REST API](/rest/api/videoanalyzer/?branch=video) の上に構築されます。

次のプラットフォームがサポートされています。

- [.NET](https://aka.ms/ava/sdk/mgt/net)
- [Java](https://aka.ms/ava/sdk/mgt/java)
- [Python](https://aka.ms/ava/sdk/mgt/python)

## <a name="client-sdks"></a>クライアント SDK

クライアント SDK を使用して、Video Analyzer エッジ モジュールの[ダイレクト メソッド][docs-direct-methods]と相互作用できます。 これらの SDK は、[Azure IoT Hub SDK][docs-iot-hub-sdks] と共に使用されるように設計されています。 IoT Hub SDK を使用してエッジ モジュールに送信できるダイレクト メソッドを表すオブジェクトを構築できます。

次のプラットフォームがサポートされています。

- [.NET](https://aka.ms/ava/sdk/client/net)
- [Python](https://aka.ms/ava/sdk/client/python)
- [Java](https://aka.ms/ava/sdk/client/java)

<!-- links -->
[docs-direct-methods]: direct-methods.md
[docs-iot-hub-sdks]: ../../iot-hub/iot-hub-devguide-sdks.md

[REST API]: https://aka.ms/ava/api/rest
---
title: Azure Peering Service 接続
description: Microsoft Azure Peering Service 接続の詳細
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: 9463a0967b9cfc25f189dd3efac80e26428866fa
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202661"
---
# <a name="peering-service-connection"></a>Peering Service 接続

通常、接続は、Peering Service を識別する論理情報セットを指します。 これは、次の属性を指定することによって定義されます。

- [論理名]
- 接続パートナー
- 接続パートナーのプライマリ サービスの場所
- 接続パートナーのバックアップ サービスの場所
- お客様の物理的な場所
- IP プレフィックス

お客様は、要件に従って、1 つまたは複数の接続を確立できます。 接続は、テレメトリ コレクションの 1 つの単位としても使用されます。 たとえば、テレメトリ アラートを選択するには、監視する接続をお客様が定義する必要があります。

> [!Note]
> Peering Service にサインアップすると、選択したプレフィックスの待ち時間の測定値を提供するために、Windows と Microsoft 365 のテレメトリが分析されます。現時点では、テレメトリ データは /24 以上のサイズのプレフィックスでのみサポートされます。
>接続テレメトリの詳細については、[Peering Service 接続テレメトリ](connection-telemetry.md)に関するページを参照してください。
>

## <a name="how-to-create-a-peering-service-connection"></a>Peering Service 接続を作成する方法

**シナリオ** - 次の図に示すように、支店が地理的に異なる場所に分散されているとします。 ここでは、お客様は、単一の接続に関連付けられている (お客様が所有しているか、サービス プロバイダーによって割り当てられた) 論理名、サービス プロバイダー (SP) 名、お客様の物理的な場所、および IP プレフィックスを指定する必要があります。  パートナーがお客様に適したサービスの場所を定義するための、プライマリおよびバックアップ サービスの場所。 他の場所の Peering Service を作成するには、このプロセスを繰り返す必要があります。

![geo 冗長接続](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 接続が地理的に米国に配置されている場合は、お客様の物理的な場所に対して状態レベルのフィルター処理が考慮されます。
>

## <a name="next-steps"></a>次のステップ

Peering Service 接続を登録する手順の詳細については、[Azure portal を使用した Peering Service の作成](azure-portal.md)に関するページを参照してください。

Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。

テレメトリにアクセスするには、[接続テレメトリへのアクセス](measure-connection-telemetry.md)に関するページを参照してください。

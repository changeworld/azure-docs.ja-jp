---
title: Media Services V2 と V3 の API アクセス
description: この記事では、Azure Media Services V2 から V3 への API アクセスの違いについて説明します。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5f3c6526139389da3bfdbc3c43cf8b6d2a1dbccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567969"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Azure Media Services V2 から v3 API への API アクセスの違い

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-2.svg)

この記事では、Azure Media Services V2 から V3 への API アクセスの違いについて説明します。

## <a name="api-access"></a>API アクセス

すべての Media Services アカウントは、V3 API にアクセスできます。 ただし、更新されたコードを既存の V2 アカウントに適用する前に、新しいアカウントで移行開発を行うことを強くお勧めします。 これは、V3 エンティティが V2 と下位互換性がないためです。 資産などの一部の V2 エンティティは、V3 との上位互換性があります。
V2 API と V3 API を混在させずに V2 に戻る場合は、既存のアカウントを引き続き使用できますが、この方法はお勧めしません。

V2 API へのアクセスは、2024 で廃止されるまで利用できます。

## <a name="create-a-v3-account"></a>V3 アカウントを作成する

移行中に、V2 に引き続きアクセスできる V3 アカウントを作成できます。  アカウントの作成は、次の方法で行うことができます。

- REST API 以前のバージョン
- ポータルでチェックボックスをオンにします。

> [!div class="mx-imgBorder"]
> [ ![ポータルでのアカウントの作成](./media/migration-guide/v-3-v-2-access-account-creation-small.png) ](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

すべての .NET、CLI、およびその他の SDK は、最新の 2020-05-01 API を対象とします。そのため、古い API バージョンを検索または構成します。

> [!NOTE]
> 2020-05-01 API で作成された新しいアカウントで V2 API を使用することはできません。

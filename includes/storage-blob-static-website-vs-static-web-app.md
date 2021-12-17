---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 11/04/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 74ee76424ccbbdcaaebb4652acbe42bf569ac86d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096935"
---
静的 Web サイトにはいくつかの制限があります。 たとえば、ヘッダーを構成する場合は、Azure Content Delivery Network (Azure CDN) を使用する必要があります。 静的 Web サイト機能自体の一部としてヘッダーを構成する方法はありません。 また、AuthN と AuthZ はサポートされません。 

これらの機能がお使いのシナリオで重要な場合は、[Azure Static Web Apps](https://azure.microsoft.com/services/app-service/static/) を使用するようにしてください。 これは静的 Web サイトに代わる優れた手段であり、コンテンツのレンダリングに Web サーバーが必要ない場合にも適しています。 ヘッダーを構成することができ、AuthN と AuthZ は完全にサポートされています。 Azure Static Web Apps では、GitHub ソースからグローバル デプロイまでのフル マネージドの継続的インテグレーションと継続的デリバリー (CI/CD) ワークフローも提供されます。

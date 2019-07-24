---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181333"
---
> [!NOTE]
> Web アプリは、20 分間アクティビティがないとタイムアウトする可能性があります。 タイマーがリセットされるのは、実際の Web アプリに要求があった場合のみです。 Azure portal でアプリの構成を表示したり、高度なツールのサイト (`https://<app_name>.scm.azurewebsites.net`) に対して要求を行っても、タイマーはリセットされません。 アプリが継続的またはスケジュールに従って Web ジョブを実行する場合は、**Always On** を有効にして、Web ジョブが確実に実行されるようにします。 この機能は、Basic、Standard、および Premium の[価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)でのみ利用できます。

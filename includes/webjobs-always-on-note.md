---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c3a653d66ddfb00833f2b90d61f08a51697fd46
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113131936"
---
> [!NOTE]
> Web アプリは非アクティブな状態が 20 分続くとタイムアウトになり、実際の Web アプリに対する要求だけがタイマーをリセットできます。 Azure portal でアプリの構成を表示したり、高度なツールのサイト (`https://<app_name>.scm.azurewebsites.net`) に対して要求を行ったりしても、タイマーはリセットされません。 Web アプリが継続的に実行するようにするか、またはスケジュールに従って実行するか、あるいはイベント ドリブン トリガーを使用するように設定する場合は、Web アプリの Azure **構成** ページで **[常にオン]** 設定を有効にします。 [常にオン] の設定は、これらの種類の WebJobs が確実に実行されるようにするのに役立ちます。 この機能は、Basic、Standard、および Premium の[価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)でのみ利用できます。

---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 09/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0d6363861a8412c7732a697f7e869504ddb106c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558514"
---
> [!NOTE]
> Web アプリは非アクティブな状態が 20 分続くとタイムアウトになり、実際の Web アプリに対する要求だけがタイマーをリセットできます。 Azure portal でアプリの構成を表示したり、高度なツールのサイト (`https://<app_name>.scm.azurewebsites.net`) に対して要求を行ったりしても、タイマーはリセットされません。 ジョブをホストしている Web アプリを継続的に実行させるか、またはスケジュールに従って実行させるか、あるいはイベント ドリブン トリガーを使用するように設定する場合は、Web アプリの Azure **構成** ページで **[常にオン]** 設定を有効にします。 [常にオン] の設定は、これらの種類の WebJobs が確実に実行されるようにするのに役立ちます。 この機能は、Basic、Standard、および Premium の[価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)でのみ利用できます。

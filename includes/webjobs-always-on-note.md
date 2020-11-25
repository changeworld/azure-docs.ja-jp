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
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009681"
---
> [!NOTE]
> Web アプリは、20 分間アクティビティがないとタイムアウトする可能性があります。 また、タイマーをリセットできるのは、実際の Web アプリに対する要求のみです。 Azure portal でアプリの構成を表示したり、高度なツールのサイト (`https://<app_name>.scm.azurewebsites.net`) に対して要求を行ったりしても、タイマーはリセットされません。 Web アプリが継続的にまたはスケジュールに従って (タイマー トリガー) Web ジョブを実行するように設定する場合は、Web アプリの Azure **構成** ページで **Always on** 設定を有効にして、Web ジョブが確実に実行されるようにします。 この機能は、Basic、Standard、および Premium の[価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)でのみ利用できます。

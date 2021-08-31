---
title: Azure Arc 対応データ サービス - リリース バージョン
description: Azure Arc 対応データ サービスのリリース日別のバージョンのログ
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/06/2021
ms.topic: conceptual
ms.openlocfilehash: 3e636fe5ff6477f78f09d940b70099b8cbf41fb9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862853"
---
# <a name="version-log"></a>バージョン ログ

次の表では、変更されるさまざまなバージョンについて時系列で説明しています。

|Date|リリース名|コンテナー イメージ タグ|CRD プレフィックスとバージョン|ARM API バージョン|`arcdata` Azure CLI 拡張機能バージョン|Arc 対応 Kubernetes Helm Chart 張機能バージョン|Azure Data Studio 用の Arc Data 拡張機能|
|---|---|---|---|---|---|---|---|
|2021 年 7 月 30 日|Arc 対応 SQL Managed Instance General Purpose および Arc 対応 SQL Server 一般提供|v1.0.0_2021-07-30|`datacontrollers`: v1beta1、v1 <br/>`exporttasks.tasks`: v1beta1、v1 <br/>`monitors`: v1beta1、v1 <br/>`sqlmanagedinstances.sql`: v1beta1、v1 <br/>`postgresqls`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`: v1beta1 <br/>`dags.sql`: v1beta1 <br/>|2021-08-01 (安定)|1.0|1.0.16701001、リリース トレイン: 安定|0.9.5|
|2021 年 8 月 3 日|7 月 30 日の一般提供に合わせた、Azure Data Studio 用の Azure Arc 拡張機能への更新|変更なし|変更なし|変更なし|変更なし|変更なし|0.9.6|

完全な CRD については、`.arcdata.microsoft.com` をプレフィックスに追加します。 

---
title: Azure Arc 対応データ サービス - リリース バージョン
description: Azure Arc 対応データ サービスのリリース日別のバージョンのログ
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/04/2021
ms.topic: conceptual
ms.openlocfilehash: ca1b31abcfb9985dadda1ece8fced0a9f70c3b04
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893920"
---
# <a name="version-log"></a>バージョン ログ

この記事では、Azure Arc 対応データ サービスの各リリースでのコンポーネント バージョンを示します。

## <a name="november-2-2021"></a>2021 年 11 月 2 日

次の表では、このリリースのコンポーネントについて説明します。

|コンポーネント  |値  |
|--------------------------------------------------------|---------|
|コンテナー イメージ タグ                                    | v1.1.0_2021-11-02 |
|CRD の名前とバージョン                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1、v1、v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1、v1、v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1、v1、v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1、v1、v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1、v2beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1、v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1、v2beta2 |
|ARM API バージョン                                         | 2021-11-01 |
|`arcdata` Azure CLI 拡張機能バージョン                   | 1.1.0、(Nov 3)、</br>1.1.1 (Nov4) |
|Arc 対応 Kubernetes Helm Chart 張機能バージョン     | 1.0.17551005 - GA からアップグレードする場合は必須 <br/><br/> 1.1.17561007 - GA+1/Nov リリース グラフ |
|Azure Data Studio 用の Arc Data 拡張機能                | 0.9.7 |

## <a name="august-3-2021"></a>2021 年 8 月 3 日

このリリースでは、7 月 30 日の一般提供に合わせ、Azure Data Studio 用の Azure Arc 拡張機能が更新されます。 次の表では、このリリースで更新されるコンポーネントについて説明します。 

|コンポーネント  |値  |
|--------------------------------------------------------|---------|
|Azure Data Studio 用の Arc Data 拡張機能                | 0.9.6 |

他のすべてのコンポーネントは、以前にリリースされたものと同じです。

## <a name="july-30-2021"></a>2021 年 7 月 30 日

このリリースでは、Azure Arc 対応 SQL Managed Instance 汎用目的と Azure Arc 対応 SQL Server の一般提供が導入されます。 次の表では、このリリースのコンポーネントについて説明します。

|コンポーネント  |値  |
|--------------------------------------------------------|---------|
|コンテナー イメージ タグ                                    | v1.0.0_2021-07-30 |
|CRD の名前とバージョン                                  |`datacontrollers.arcdata.microsoft.com`: v1beta1、v1 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1、v1 <br/>`monitors.arcdata.microsoft.com`: v1beta1、v1 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1、v1 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`: v1beta1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1 <br/> |
|ARM API バージョン                                         | 2021-08-01 (安定) |
|`arcdata` Azure CLI 拡張機能バージョン                   | 1.0 |
|Arc 対応 Kubernetes Helm Chart 張機能バージョン     | 1.0.16701001、リリース トレイン: 安定 |
|Azure Data Studio 用の Arc Data 拡張機能                | 0.9.5 |

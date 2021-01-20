---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605210"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| Azure アプリのプッシュ | アクション グループあたり 10 個のアプリ アクション。 | 既定値と同じ |
| Email | アクション グループの 1,000 個のメール アクション。<br>1 時間で 100 件以下の電子メール。<br>[レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | 既定値と同じ |
| ITSM | アクション グループの 10 個の ITSM アクション。 | 既定値と同じ | 
| ロジック アプリ | アクション グループの 10 個のロジック アプリ アクション。 | 既定値と同じ |
| Runbook | アクション グループの 10 個の Runbook アクション。 | 既定値と同じ |
| sms | アクション グループの 10 個の SMS アクション。<br>5 分ごとに SMS メッセージ 1 件以下。<br>[レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | 既定値と同じ |
| 音声 | アクション グループの 10 個の音声アクション。<br>5 分ごとに 1 件以下の音声通話。<br>[レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | 既定値と同じ |
| Webhook | アクション グループの 10 個の Webhook アクション。  Webhook の最大呼び出し数は、サブスクリプションごとに毎分 1500 個です。 その他の制限は[アクション固有の情報](../articles/azure-monitor/platform/action-groups.md#action-specific-information)でご覧いただけます。  | 既定値と同じ |

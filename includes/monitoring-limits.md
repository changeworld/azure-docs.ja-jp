---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66271550"
---
| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| 自動スケールの設定 |サブスクリプションあたりのリージョンごとに 100。 | 既定値と同じ。 |
| メトリック アラート (クラシック) |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます。 |
| メトリック アラート |サブスクリプション (パブリック クラウド内) あたり 1000 個のアクティブなアラート ルール、Azure China および Azure Government 内のサブスクリプションあたり 100 個のアクティブなアラート ルール。 | サポートに問い合わせます。 |
| アクティビティ ログ アラート | サブスクリプションごとに 100 個のアクティブなアラート ルール。 | 既定値と同じ。 |
| ログ アラート | 512 | サポートに問い合わせます。 |
| アクション グループ |サブスクリプションごとに 2,000 のアクション グループ。 | サポートに問い合わせます。 |

**アクション グループ固有の制限**

| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| Azure アプリのプッシュ | アクション グループあたり 10 個のアプリ アクション。 | サポートに問い合わせます。 |
| Email | アクション グループの 1,000 個のメール アクション。 [レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| ITSM | アクション グループの 10 個の ITSM アクション。 | サポートに問い合わせます。 | 
| ロジック アプリ | アクション グループの 10 個のロジック アプリ アクション。 | サポートに問い合わせます。 |
| Runbook | アクション グループの 10 個の Runbook アクション。 | サポートに問い合わせます。 |
| sms | アクション グループの 10 個の SMS アクション。 [レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| 音声 | アクション グループの 10 個の音声アクション。 [レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| Webhook | アクション グループの 10 個の Webhook アクション。  Webhook の最大呼び出し数は、サブスクリプションごとに毎分 1500 個です。 その他の制限は[アクション固有の情報](../articles/azure-monitor/platform/action-groups.md#action-specific-information)でご覧いただけます。  | サポートに問い合わせます。 |

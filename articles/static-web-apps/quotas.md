---
title: Azure Static Web Apps のプレビューのクォータ
description: Azure Static Web Apps のプレビューに関連付けられているクォータについて説明します
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095171"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Azure Static Web Apps のプレビューのクォータ

Azure Static Web Apps のプレビューには、次のクォータがあります。

> [!IMPORTANT]
> Azure Static Web Apps はパブリック プレビュー段階であり、運用環境での使用は想定されていません。

| 機能                     | Free プラン        |
|-----------------------------|------------------|
| 含まれる帯域幅          | 100 GB/月 |
| 超過帯域幅           | 使用不可      |
| Azure サブスクリプションあたりのアプリ数 | 10               |
| アプリのサイズ                    | 250 MB           |
| 実稼働前の環境数 | 3                |
| カスタム ドメイン              | 1                |
| 承認 (カスタム ロールとルーティング規則を使用) | カスタム ロールに属することができるエンドユーザーは最大 25 名 |
| Azure Functions             | 利用可能        |
| SLA                         | なし             |

## <a name="github-storage"></a>GitHub ストレージ

GitHub Actions とパッケージでは、独自のクォータ セットを持つ GitHub ストレージが使用されます。 Azure Static Web Apps サイトを作成すると、デプロイ後でも GitHub によってサイトのアーティファクトが格納されます。

詳細については、次のリソースを参照してください。

- [Actions ストレージ領域の管理](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [GitHub Actions の課金について](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [GitHub Actions の使用制限の管理](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>次のステップ

- [概要](overview.md)

---
title: Azure Static Web Apps のクォータ
description: Azure Static Web Apps に関連付けられているクォータについて説明します
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5030b0fb4e714be221e28e590c463186ec81ad04
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515587"
---
# <a name="quotas-in-azure-static-web-apps"></a>Azure Static Web Apps のクォータ

Azure Static Web Apps には、次のクォータがあります。

| 機能                     | Free プラン        | Standard プラン |
|-----------------------------|------------------|---------------|
| 含まれる帯域幅          | サブスクリプションあたり 1 か月 100 GB | サブスクリプションあたり 1 か月 100 GB |
| 超過帯域幅           | 使用不可      | GB あたり $0.20 |
| Azure サブスクリプションあたりのアプリ数 | 10               | 無制限 |
| アプリのサイズ                    | 250 MB           | 500 MB |
| プランのサイズ                   | 単一のデプロイでは最大 500 MB のアプリ サイズ、すべてのステージング環境と運用環境では最大 0.50 GB  | 単一のデプロイでは最大 500 MB のアプリ サイズ、すべてのステージング環境と運用環境を合わせて最大 2.00 GB |
| 実稼働前の環境数 | 3                | 10 |
| カスタム ドメイン              | アプリあたり 2        | アプリあたり 5 |
| 承認 (カスタム ロールとルーティング規則を使用) | カスタム ロールに属することができるエンドユーザーは最大 25 名 | カスタム ロールに属することができるエンドユーザーは最大 25 名 |

## <a name="github-storage"></a>GitHub ストレージ

GitHub Actions とパッケージでは、独自のクォータ セットを持つ GitHub ストレージが使用されます。 Azure Static Web Apps サイトを作成すると、デプロイ後でも GitHub によってサイトのアーティファクトが格納されます。

詳細については、次のリソースを参照してください。

- [Actions ストレージ領域の管理](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [GitHub Actions の課金について](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [GitHub Actions の使用制限の管理](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>次のステップ

- [概要](overview.md)

---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: ba0c68589f7ea4b32970e3ad81fea5175b998dc7
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629155"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>追加される Azure セキュリティ ベンチマークに関連する推奨事項 (プレビュー)

| 側面 | 詳細 |
|---------|---------|
|Announcement date (発表日) | 2020 年 10 月 26 日  |
|この変更の日付  |  2020 年 11 月 |
|影響     | さらに多くの推奨事項がレビューされる可能性があります。<br>すぐにはセキュリティ スコアに影響しません。プレビューの推奨事項は、セキュリティ スコアに影響しません。<br>すぐにはリソースの正常性状態に影響しません。プレビューの推奨事項によってリソースが "異常" になることはありません。|
|  |  |

Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 [Azure セキュリティ ベンチマークの詳細を確認してください](../security/benchmarks/introduction.md)。

ベンチマークのカバレッジを拡大するために、次の 29 個の新しい推奨事項が Security Center に追加されます。

プレビューの推奨事項によってリソースが異常な状態になることはありません。これらの推奨事項は、セキュリティ スコアの計算には含まれません。 これらの推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復してください。 これらの推奨事項に対応する方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください。

- 仮想マシンに対して Azure Backup を有効にする必要がある
- SQL サーバーの監査のリテンション期間は少なくとも 90 日に設定する必要がある
- App Service の診断ログを有効にする必要がある 
- MySQL データベース サーバーで [SSL 接続を強制する] が有効でなければならない
- PostgreSQL データベース サーバーで [SSL 接続を強制する] が有効でなければならない
- API アプリでは FTPS を必須とする
- 関数アプリでは FTPS を必須とする
- Web アプリでは FTPS を必須とする
- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある
- Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある
- Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある
- API アプリに対して Java を最新バージョンに更新する必要がある
- 関数アプリに対して Java を最新バージョンに更新する必要がある
- Web アプリに対して Java を最新バージョンに更新する必要がある
- API アプリではマネージド ID を使用する必要がある
- 関数アプリではマネージド ID を使用する必要がある
- Web アプリではマネージド ID を使用する必要がある
- API アプリに対して PHP を最新バージョンに更新する必要がある
- Web アプリに対して PHP を最新バージョンに更新する必要がある
- MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある
- MySQL サーバーに対してプライベート エンドポイントを有効にする必要がある
- PostgreSQL サーバーに対してプライベート エンドポイントを有効にする必要がある
- API アプリに対して Python を最新バージョンに更新する必要がある
- 関数アプリに対して Python を最新バージョンに更新する必要がある
- Web アプリに対して Python を最新バージョンに更新する必要がある
- API アプリに対して TLS を最新バージョンに更新する必要がある
- 関数アプリに対して TLS を最新バージョンに更新する必要がある
- Web アプリに対して TLS を最新バージョンに更新する必要がある
- Web アプリではすべての受信要求に対して SSL 証明書を要求する必要がある

関連リンク:

- [Azure セキュリティ ベンチマークについての詳細情報](../security/benchmarks/introduction.md)
- [Azure API アプリについての詳細情報](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 関数アプリについての詳細情報](../azure-functions/functions-overview.md)
- [Azure Web アプリについての詳細情報](../app-service/overview.md)
- [Azure Database for MariaDB についての詳細情報](../mariadb/overview.md)
- [Azure Database for MySQL についての詳細情報](../mysql/overview.md)
- [Azure Database for PostgreSQL についての詳細情報](../postgresql/overview.md)

## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。
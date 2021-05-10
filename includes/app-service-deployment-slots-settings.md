---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: ed53cb60f6fb3bca93900941a8a6a53bed99d779
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073402"
---
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。 構成要素には、スワップを経ても内容が反映される (スロット固有でない) ものもあれば、スワップ後に同じスロットに残されている (スロット固有の) ものもあります。 次の一覧では、スロットのスワップ時に変更される設定を示します。

**スワップされる設定**:

* 一般設定 (フレームワーク バージョン、32/64 ビット、Web ソケットなど)
* アプリ設定 (スロット固有として構成可能)
* 接続文字列 (スロット固有として構成可能)
* ハンドラー マッピング
* パブリック証明書
* Web ジョブ コンテンツ
* ハイブリッド接続 *
* サービス エンドポイント *
* Azure Content Delivery Network *

アスタリスク (*) 記号付きの機能は、スワップされない予定です。 

**スワップされない設定**:

* 発行エンドポイント
* カスタム ドメイン名
* パブリックでない証明書と TLS/SSL 設定
* スケールの設定
* Web ジョブ スケジューラ
* IP 制限
* 常時接続
* 診断設定
* クロスオリジン リソース共有 (CORS)
* 仮想ネットワークの統合
* パスのマッピング
* サフィックス _EXTENSION_VERSION で終わる設定

> [!NOTE]
> これらの設定をスワップ可能にするには、アプリのすべてのスロットでアプリ設定 `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` を追加し、その値を `0` または `false` に設定します。 これらの設定は、すべてがスワップ可能か、まったくスワップ可能でないかのどちらかです。 一部の設定だけをスワップ可能にして、他を不可にすることはできません。

> スワップされていない設定に適用されている特定のアプリ設定もスワップされません。 たとえば、診断の設定はスワップされないため、`WEBSITE_HTTPLOGGING_RETENTION_DAYS` や `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` などの関連するアプリ設定もスワップされません。これは、スロット設定として表示されない場合でも同様です。
>

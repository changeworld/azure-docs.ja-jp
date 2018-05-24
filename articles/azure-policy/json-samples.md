---
title: ポリシー テンプレートのサンプル
description: Azure Policy の JSON サンプル
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4b9096c1fb0d9ee74849e259a6e0af2486c5d29b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195126"
---
# <a name="templates-for-azure-policy"></a>Azure Policy のテンプレート

次の表に、Azure Policy の json テンプレートへのリンクを示します。 これらのサンプルは、[Azure Policy のサンプル リポジトリ](https://github.com/Azure/azure-policy)にあります。

| | |
|---|---|
|**Compute**||
| [承認された VM イメージ](scripts/allowed-custom-images.md) | 承認されたカスタム イメージのみを環境にデプロイする必要があります。 承認されたイメージ ID の配列を指定します。 |
| [VM が管理ディスクを使っていない場合の監査](scripts/create-vm-managed-disk.md) | 管理ディスクを使用していない仮想マシンが作成されたときに監査が行われます。|
| [拡張機能が存在しない場合の監査](scripts/audit-ext-not-exist.md) | 仮想マシンに拡張機能がデプロイされていない場合に監査が行われます。 デプロイされたかどうかを確認する拡張機能の公開元と種類を指定します。 |
| [リソース グループのカスタム VM イメージの許可](scripts/allow-custom-vm-image.md) |  カスタム イメージが承認されたリソース グループに含まれている必要があります。 承認されたリソース グループの名前を指定します。 |
| [Azure Hybrid Use Benefit の拒否](scripts/deny-hybrid-use.md) | Azure Hybrid Use Benefit (AHUB) の使用を禁止します。 オンプレミス ライセンスの使用を許可したくない場合に利用します。 |
| [許可されない VM 拡張機能](scripts/not-allowed-vm-ext.md) | 指定された拡張機能の使用を禁止します。 禁止されている拡張機能の種類を格納している配列を指定します。 |
| [特定の VM プラットフォーム イメージのみの許可](scripts/allow-certain-vm-image.md) | 仮想マシンが特定のバージョンの UbuntuServer を使用する必要があります。 |
| [管理ディスクを使用した VM の作成](scripts/use-managed-disk-vm.md) | 仮想マシンで管理ディスクを使用する必要があります。|
|**監視**||
| [診断設定の監査](scripts/audit-diag-setting.md) | 指定されたリソースの種類で診断設定が有効になっていない場合に監査を行います。 リソースの種類の配列を指定して、診断設定が有効になっているかどうかを確認します。 |
|**名前およびテキストの表記規則**||
| [複数の名前パターンを許可する](scripts/allow-multiple-name-patterns.md) | 多数の名前パターンのいずれかをリソースに使うことを許可します。 |
| [類似パターンを要求する](scripts/enforce-like-pattern.md) | リソース名がパターンの like 条件を満たすことを保証します。 |
| [一致パターンを要求する](scripts/enforce-match-pattern.md) | リソース名が名前付けパターンと一致することを保証します。 |
| [タグ一致パターンを要求する](scripts/enforce-tag-match-pattern.md) | タグの値がテキストのパターンと一致することを保証します。 |
|**ネットワーク**||
| [許可されたアプリケーション ゲートウェイ SKU](scripts/allowed-app-gate-sku.md) | アプリケーション ゲートウェイで適切な SKU を使用する必要があります。 承認された SKU の配列を指定します。 |
| [リージョンで Network Watcher が有効になっていない場合の監査](scripts/net-watch-not-enabled.md) | 特定のリージョンで Network Watcher が有効になっていない場合に監査を行います。 リージョンの名前を指定して、Network Watcher が有効になっているかどうかを確認します。 |
| [すべての NIC 上の NSG X](scripts/nsg-on-nic.md) | 特定のネットワーク セキュリティ グループが、すべての仮想ネットワーク インターフェイスで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [すべてのサブネット上の NSG X](scripts/nsg-on-subnet.md) | 特定のネットワーク セキュリティ グループが、すべての仮想サブネットで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [許可された Express Route の帯域幅](scripts/allowed-er-band.md) | Express Route で、指定した帯域幅の設定を使用する必要があります。 Express Route に対して指定できる SKU の配列を指定します。 |
| [Express Route に対して許可されたピアリングの場所](scripts/allowed-peering-er.md) | Express Route で、指定したピアリングの場所を使用する必要があります。 許可されているピアリングの場所の配列を指定します。 |
| [許可された Express Route の SKU](scripts/allowed-er-skus.md) | 承認された SKU が Express Route で使用される必要があります。 許可された SKU の配列を指定します。 |
| [許可されたロード バランサーの SKU](scripts/allowed-lb-skus.md) | 承認された SKU がロード バランサーで使用される必要があります。 許可された SKU の配列を指定します。 |
| [ER ネットワークへのネットワーク ピアリングがない](scripts/no-peering-er-net.md) | 指定されたリソース グループ内のネットワークに、ネットワーク ピアリングを関連付けることを禁止します。 集中管理されているネットワーク インフラストラクチャとの接続を防ぐために使用します。 関連付けを防ぐリソース グループの名前を指定します。 |
| [ユーザー定義ルート テーブルがない](scripts/no-user-def-route-table.md)  |仮想ネットワークがユーザー定義のルート テーブルを使ってデプロイされることを禁止します。 |
| [許可された仮想ネットワーク ゲートウェイ SKU](scripts/allowed-vn-gate-sku.md) | 仮想ネットワーク ゲートウェイが適切な SKU とゲートウェイの種類を使用する必要があります。 承認された SKU の配列と、承認されたゲートウェイの種類の配列を指定します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](scripts/use-approved-subnet-vm-nics.md) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](scripts/use-approved-vnet-vm-nics.md) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |
|**タグ**||
| [課金タグ ポリシーのイニシアティブ](scripts/billing-tags-policy-init.md) | コスト センターと製品名に対して指定したタグの値が必要です。 組み込みのポリシーを使用して、必要なタグを強制的に適用します。 タグに対して必須の値を指定します。  |
| [リソース グループへのタグと値の強制](scripts/enforce-tag-rg.md) | 1 つのリソース グループに 1 つのタグと値が必要です。 必要なタグ名と値を指定します。  |
|**SQL**||
| [SQL DB レベルの監査設定の監査](scripts/audit-sql-db-audit-setting.md) | SQL データベースの監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。  |
| [Transparent Data Encryption の状態の監査](scripts/audit-trans-data-enc-status.md) | SQL データベースの Transparent Data Encryption が有効ではない場合にその監査を行います。  |
| [DB レベルの脅威検出設定の監査](scripts/audit-db-threat-det-setting.md) | SQL データベース セキュリティ アラート ポリシーが特定の状態に設定されていない場合、このポリシーの監査を行います。 脅威検出を有効または無効のどちらにするかを指示する値を指定します。  |
| [SQL Server レベルの監査設定の監査](scripts/audit-sql-ser-leve-audit-setting.md) | SQL Server の監査設定が指定された設定と一致しない場合に、この設定の監査を行います。 監査設定を有効または無効のどちらにするべきかを指示する値を指定します。 |
| [サーバー レベルの脅威検出設定の監査](scripts/audit-sql-ser-threat-det-setting.md) | SQL データベース セキュリティ アラート ポリシーが特定の状態に設定されていない場合、このポリシーの監査を行います。 脅威検出を有効または無効のどちらにするかを指示する値を指定します。  |
| [Azure Active Directory 管理者不在の監査](scripts/audit-no-aad-admin.md) | Azure Active Directory 管理者が SQL Server に割り当てられていない場合に、監査を行います。 |
| [許可された SQL DB の SKU](scripts/allowed-sql-db-skus.md) | 承認された SKU が SQL データベースで使用される必要があります。 許可された SKU ID の配列または許可された SKU 名の配列を指定します。 |
|**Storage**||
| [ストレージ アカウントおよび仮想マシンに対して許可された SKU](scripts/allowed-skus-storage.md) | 承認された SKU がストレージ アカウントと仮想マシンで使用される必要があります。 組み込みのポリシーを使用して、承認された SKU を確認します。 承認された仮想マシンの SKU の配列と、承認されたストレージ アカウントの SKU の配列を指定します。 |
| [ストレージ アカウントのみに対する https トラフィックの確認](scripts/ensure-https-stor-acct.md) | HTTPS トラフィックを使用するストレージ アカウントが必要です。  |
| [ストレージ アカウントのクール アクセス層の拒否](scripts/deny-cool-access-tiering.md) | BLOB ストレージ アカウントでのクール アクセス層の使用を禁止します。  |
| [ストレージ ファイルの暗号化の確認](scripts/ensure-store-file-enc.md) | ファイルの暗号化がストレージ アカウントに対して有効になっている必要があります。  |
|**組み込みのポリシー**||
| [許可される場所](scripts/allowed-locs.md) | すべてのリソースが承認済みの場所にデプロイされる必要があります。 承認された場所の配列を指定します。  |
| [許可されるリソースの種類](scripts/allowed-res-types.md) | 必ず承認されたリソースの種類のみがデプロイされるようにします。 許可されているリソースの種類の配列を指定します。  |
| [許可されるストレージ アカウントの SKU](scripts/allowed-stor-acct-skus.md) | ストレージ アカウントが適切な SKU を使用する必要があります。 承認された SKU の配列を指定します。 |
| [タグとその既定値の適用](scripts/apply-tag-def-val.md) | 指定されたタグがない場合、そのタグ名と値を付加します。 適用するタグ名と値を指定します。  |
| [SQL Database の暗号化の監査](scripts/sql-database-encryption-audit.md) | SQL Database で Transparent Data Encryption が有効になっていないかどうかを監査します。 |
| [SQL Server の監査設定の監査](scripts/sql-server-audit.md) | 監査設定が有効になっているかどうかに基づいて SQL Server を監査します。 |
| [Data Lake Store での暗号化の適用](scripts/enforce-datalakestore-encryption.md) | 暗号化が有効になっていない Data Lake Store アカウントを拒否します。 |
| [タグとその値を強制](scripts/enforce-tag-val.md) | 指定したタグの名前と値が必要です。 強制的に適用するタグ名と値を指定します。  |
| [許可されないリソースの種類](scripts/not-allowed-res-type.md) | 指定されたリソースの種類のデプロイを禁止します。 ブロックするリソースの種類の配列を指定します。  |
| [SQL Server バージョン 12.0 が必要](scripts/req-sql-12.md) | SQL Server でバージョン 12.0 を使用する必要があります。  |
| [ストレージ アカウントの暗号化が必要](scripts/req-store-acct-enc.md) | ストレージ アカウントで BLOB 暗号化を使用する必要があります。  |

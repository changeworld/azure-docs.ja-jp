---
title: Azure Migrate のコレクター アプライアンスのアップグレード | Microsoft Docs
description: Azure Migrate Collector アプライアンスのアップグレードに関する情報を提供します。
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 88077ac965b2abb69be145f29cbadca2ff1128d6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836646"
---
# <a name="collector-update-release-history"></a>コレクターの更新プログラムのリリース履歴

この記事には、[Azure Migrate](migrate-overview.md) の Collector アプライアンスのアップグレード情報がまとめられています。

Azure Migrate Collector は、Azure に移行する前の評価を目的として、オンプレミス vCenter 環境を検出するために使用される軽量アプライアンスです。 [詳細情報](concepts-collector.md)。

## <a name="continuous-discovery-upgrade-versions"></a>継続的な検出:アップグレード バージョン

継続的な検出アプライアンスのアップグレードは、まだ利用できません。

## <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>1 回限りの検出 (現在は非推奨):以前のアップグレード バージョン

> [!NOTE]
> 1 回限りの検出アプライアンスは推奨されなくなりました。この方法は、パフォーマンス データ ポイントの可用性と収集された平均パフォーマンス カウンターの vCenter Server の統計設定に依存しており、その結果、Azure への移行のために VM のサイズが小さくなったためです。

### <a name="version-10916-released-on-10292018"></a>バージョン 1.0.9.16 (2018 年 10 月 29 日リリース)

アプライアンスの設定中に発生する PowerCLI の問題の修正プログラムが含まれています。

アップグレード [パッケージ 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>バージョン 1.0.9.14

アップグレード [パッケージ 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>バージョン 1.0.9.13

アップグレード [パッケージ 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="run-an-upgrade"></a>アップグレードの実行

OVA を再度ダウンロードせずに、コレクターを最新バージョンにアップグレードできます。

1. 下記のリストで、最新のアップグレード パッケージをダウンロードします。
2. ダウンロードした修正プログラムが安全であることを確認するために、管理者コマンド ウィンドウを開き、以下のコマンドを実行して、zip ファイルのハッシュを生成します。 生成されたハッシュは、その特定のバージョンのハッシュと一致する必要があります。

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    例:**C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Collector アプライアンス VM に zip ファイルをコピーします。
4. Zip ファイルを右クリックして、**[すべて展開]** を選択します。
5. **[Setup.ps1]** > **[Run with PowerShell]** を右クリックし、インストールの指示に従います。


## <a name="next-steps"></a>次の手順

- Collector アプライアンスについて、[さらに詳しく学習](concepts-collector.md)します。
- VMware VM の[評価を実行](tutorial-assessment-vmware.md)します。

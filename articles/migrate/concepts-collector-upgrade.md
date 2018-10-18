---
title: Azure Migrate のコレクター アプライアンスのアップグレード | Microsoft Docs
description: Azure Migrate Collector アプライアンスのアップグレードに関する情報を提供します。
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 6080096194ec5b11f1d5593fcbb6732d1551667f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431394"
---
# <a name="collector-update-release-history"></a>コレクターの更新プログラムのリリース履歴

この記事には、[Azure Migrate](migrate-overview.md) の Collector アプライアンスのアップグレード情報がまとめられています。

Azure Migrate Collector は、Azure に移行する前の評価を目的として、オンプレミス vCenter 環境を検出するために使用される軽量アプライアンスです。 [詳細情報](concepts-collector.md)。


## <a name="one-time-discovery-upgrade-versions"></a>1 回限りの検出: アップグレード バージョン

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

### <a name="version-10911"></a>バージョン 1.0.9.11

アップグレード [パッケージ 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>バージョン 1.0.9.7

アップグレード [パッケージ 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>継続的な検出: アップグレード バージョン

継続的な検出アプライアンスのアップグレードは、まだ利用できません。

## <a name="run-an-upgrade"></a>アップグレードの実行

OVA を再度ダウンロードせずに、コレクターを最新バージョンにアップグレードできます。

1. 下記のリストで、最新のアップグレード パッケージをダウンロードします。
2. ダウンロードした修正プログラムが安全であることを確認するために、管理者コマンド ウィンドウを開き、以下のコマンドを実行して、zip ファイルのハッシュを生成します。 生成されたハッシュは、その特定のバージョンのハッシュと一致する必要があります。

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    たとえば、**C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256** のようにします。
3. Collector アプライアンス VM に zip ファイルをコピーします。
4. Zip ファイルを右クリックして、**[すべて展開]** を選択します。
5. **[Setup.ps1]** > **[Run with PowerShell]** を右クリックし、インストールの指示に従います。


## <a name="next-steps"></a>次の手順

- Collector アプライアンスについて、[さらに詳しく学習](concepts-collector.md)します。
- VMware VM の[評価を実行](tutorial-assessment-vmware.md)します。

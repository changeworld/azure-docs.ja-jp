---
title: Azure SAP HANA のオペレーティング システムのアップグレード (ラージ インスタンス) | Microsoft Docs
description: Azure の SAP HANA のオペレーティング システムのアップグレードを実行します｡
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077747"
---
# <a name="operating-system-upgrade"></a>オペレーティング システムのアップグレード
このドキュメントでは､HANA ラージ インスタンスでのオペレーティング システムについての詳細を説明します｡

>[!NOTE]
>OS のアップグレードはお客様の責任です｡Microsoft の運用サポートができることは､アップグレード中にどのような点に注意を払うべきかについてお答えすることです｡ アップグレードを計画する前にオペレーティング システムのベンダーにも相談するようにしてください｡

HLI ユニットのプロビジョニング時､Microsoft の運用チームはオペレーティング システムをインストールします｡ 時間の経過と共に､HLI ユニット上のオペレーティング システムの保守が必要になります (パッチ適用､チューニング､アップグレードなど)｡

オペレーティング システムに対して主要な変更 (OS のアップグレードなど) を行うにあたっては､次の互換性マトリックスを**考慮する必要**があります｡ アップグレードなどの主要なオペレーティング システム アクティビティを開始する前に､相談するサポート チケットを開くことで､Microsoft 運用チームに **問い合わせる必要**もあります｡

各種 Linux バージョンと各種 SAP HANA バージョンのサポート マトリックスについては、[SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581) を確認してください。

HLI については､下記互換性がテスト済みです｡ ご使用の HLI サーバーが互換性マトリックスにない場合は､Microsoft 運用サポートにお問い合わせください｡

## <a name="for-type-i-class-sku-category"></a>タイプ I クラスの SKU カテゴリ

| 構成 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| サーバーのファームウェア | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC のバージョン | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC のバージョン | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | 無効 | 無効 | 無効 | 無効 |
| カーネル バージョン | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>タイプ II クラスの SKU カテゴリ

| 構成 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| RMC ファームウェアのバージョン | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| BMC ファームウェアのバージョン | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| SFS (Software Foundation Server) のバージョン | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e のバージョン    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| カーネル バージョン    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>既知の問題

以下は､アップグレード中にいくつかある一般的な既知の問題です｡
- タイプ II クラスの SKU で､OS のアップグレード後に SFS が削除されます｡ OS のアップグレード後に互換性のある SFS を再インストールする必要があります｡
- イーサネット カードのドライバー (ENIC および FNIC) が古いバージョンにロールバックします｡ アップグレード後に互換性のあるドライバー バージョンを再インストールする必要があります｡

## <a name="next-steps"></a>次の手順
- OS バックアップのタイプ I SKU クラスについては､[バックアップと復元](hana-overview-high-availability-disaster-recovery.md)を参照してください｡
- タイプ II の SKU クラスについては[タイプ II SKU の OS バックアップ](os-backup-type-ii-skus.md) を参照してください｡
---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2021
ms.author: tamram
ms.custom: include file, references_regions
ms.openlocfilehash: 300a216e02182e3e2fe675795ff64942d8e98ed8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096918"
---
次の表では、Azure の汎用 v1 (GPv1)、汎用 v2 (GPv2)、および BLOB ストレージのアカウントに関する既定の制限について説明します。 "*受信*" 制限は、ストレージ アカウントに送信されるすべてのデータを指します。 *送信* 制限は、ストレージ アカウントから受信するすべてのデータを指します。

Microsoft では、ほとんどのシナリオで GPv2 ストレージ アカウントを使用することをお勧めしています。 GPv1 または BLOB ストレージ アカウントは GPv2 アカウントに簡単にアップグレードできます。そのとき、ダウンタイムは発生せず、データをコピーする必要はありません。 詳細については、[GPv2 ストレージ アカウントへのアップグレード](../articles/storage/common/storage-account-upgrade.md)に関する記事を参照してください。

> [!NOTE]
> より高い容量とイングレスの制限を要求できます。 増加を依頼するには、[Azure サポート](https://azure.microsoft.com/support/faq/)にお問い合わせください。

| リソース | 制限 |
|--|--|
| サブスクリプションあたりの各リージョンのストレージ アカウント数 (Standard および Premium のストレージ アカウントを含む)。 | 250 |
| 既定のストレージ アカウントの最大容量 | 5 PiB <sup>1</sup> |
| ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数。 | 制限なし |
| 既定のストレージ アカウントあたりの最大要求レート | 1 秒あたり 20,000 要求<sup>1</sup> |
| 次のリージョン (LRS/GRS) の汎用 v2 および BLOB ストレージ アカウントあたりの既定の最大イングレス:<br /><ul><li>オーストラリア東部</li><li>米国中部</li><li>東アジア</li><li>米国東部 2</li><li>東日本</li><li>韓国中部</li><li>北ヨーロッパ</li><li>米国中南部</li><li>東南アジア</li><li>英国南部</li><li>西ヨーロッパ</li><li>米国西部</li></ul> | 60 Gbps<sup>1</sup> |
| 次のリージョン (ZRS) の汎用 v2 および BLOB ストレージ アカウントあたりの既定の最大イングレス:<br /><ul><li>オーストラリア東部</li><li>米国中部</li><li>米国東部</li><li>米国東部 2</li><li>東日本</li><li>北ヨーロッパ</li><li>米国中南部</li><li>東南アジア</li><li>英国南部</li><li>西ヨーロッパ</li><li>米国西部 2</li></ul> | 60 Gbps<sup>1</sup> |
| 上記にリストされていないリージョンの汎用 v2 および BLOB ストレージ アカウントあたりの既定の最大イングレス: | 25 Gbps<sup>1</sup> |
| 汎用 v1 ストレージ アカウントの既定の最大イングレス (すべてのリージョン) | 10 Gbps<sup>1</sup> |
| 次のリージョン (LRS/GRS) の汎用 v2 および BLOB ストレージ アカウントあたりの既定の最大エグレス:<br /><ul><li>オーストラリア東部</li><li>米国中部</li><li>東アジア</li><li>米国東部 2</li><li>東日本</li><li>韓国中部</li><li>北ヨーロッパ</li><li>米国中南部</li><li>東南アジア</li><li>英国南部</li><li>西ヨーロッパ</li><li>米国西部</li></ul> | 120 Gbps<sup>1</sup> |
| 次のリージョン (ZRS) の汎用 v2 および BLOB ストレージ アカウントあたりの既定の最大エグレス: <ul><li>オーストラリア東部</li><li>米国中部</li><li>米国東部</li><li>米国東部 2</li><li>東日本</li><li>北ヨーロッパ</li><li>米国中南部</li><li>東南アジア</li><li>英国南部</li><li>西ヨーロッパ</li><li>米国西部 2</li></ul> | 120 Gbps<sup>1</sup> |
| 上記にリストされていないリージョンの汎用 v2 および BLOB ストレージ アカウントあたりの既定の最大エグレス: | 50 Gbps<sup>1</sup> |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国リージョン) | RA-GRS/GRS が有効な場合は 20 Gbps、LRS/ZRS<sup>2</sup> の場合は 30 Gbps |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS<sup>2</sup> の場合は 15 Gbps |
| ストレージアカウントごとの IP アドレス規則の最大数 | 200 |
| ストレージアカウントごとの仮想ネットワーク規則の最大数 | 200 |
| ストレージアカウントごとのリソース インスタンス ルールの最大数 | 200 |
| ストレージ アカウントごとのプライベート エンドポイントの最大数 | 200 |

<sup>1</sup> Azure Storage 標準アカウントは、要求によるより高い容量制限とより高いイングレスおよびエグレス制限をサポートします。 アカウント制限の引き上げを希望する場合は、[Azure サポートにお問い合わせください](https://azure.microsoft.com/support/faq/)。

<sup>2</sup> お使いのストレージ アカウントの読み取りアクセスで geo 冗長ストレージ (RA-GRS) または geo ゾーン冗長ストレージ (RA-GZRS) を有効にしている場合、第 2 の場所のエグレス ターゲットが第 1 の場所のエグレス ターゲットと同じになります。 詳細については、「[Azure Storage のレプリケーション](../articles/storage/common/storage-redundancy.md)」をご覧ください。

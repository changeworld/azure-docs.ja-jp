---
title: Oracle 用の BareMetal のプロビジョニング
description: Oracle 用の BareMetal インフラストラクチャのプロビジョニングについて説明します。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 59618d43d720fe4964d1e2971865f83572e681d9
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578553"
---
# <a name="provision-baremetal-for-oracle"></a>Oracle 用の BareMetal のプロビジョニング

この記事では、Oracle ワークロード用の BareMetal インフラストラクチャ インスタンスをプロビジョニングする方法を説明します。 

BareMetal インスタンスをプロビジョニングする最初の手順は、Microsoft CSA を使用することです。 CSA は、固有のワークロードのニーズと、デプロイしているアーキテクチャ (単一インスタンス、1 ノード RAC、RAC など) に基づいて支援します。 これらのトポロジの詳細については、「[Oracle 用 BareMetal インフラストラクチャのアーキテクチャ](oracle-baremetal-architecture.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * 有効な Azure サブスクリプション
> * Microsoft Premier サポート契約
> * Red Hat Enterprise Linux 7.6 用のライセンス
> * Oracle サポート契約 
> * Oracle 用のライセンスおよびソフトウェア インストール コンポーネント
> * **オンプレミスで Azure に** 接続されている ExpressRoute (**必要に応じて**、オンプレミスから Oracle データベースに直接接続するように ExpressRoute Global Reach を構成します)   
> * 仮想ネットワーク
> * ゲートウェイの作成
> * 仮想ネットワーク内の仮想マシン (VM) (ジャンプ ボックス)

## <a name="information-to-provide-microsoft-operations"></a>Microsoft の操作を提供するための情報

次の情報を CSA に提供する必要があります。

1. 仮想ネットワークのアドレス空間。 この範囲は /24 サブネット (例: 10.11.0.0/24) である必要があります。
2. P2P の範囲。 この範囲は /29 サブネット (例: 10.12.0.0/29) である必要があります。
3. サーバーの IP アドレス プール。 推奨される範囲は /24 です (例: 10.13.0.0/24)。
4. サーバーの IP アドレス。 サーバーの IP アドレス プールから IP アドレスを選択します。

    > [!Note] 
    > 最初の 30 個の IP アドレスは、Microsoft インフラストラクチャの構成用に予約されています。 したがって、この例では、ブレードに使用できる最初の IP アドレスは 10.13.0.30 です。

5. Azure リージョン (例: West US2) が必要です。
6. BareMetal インフラストラクチャ SKU (例: S192 (2 台のコンピューター))が必要です。

## <a name="storage-requirements"></a>ストレージの要件

将来の成長に基づくストレージ ニーズを含め、プロビジョニング要求中のストレージ ニーズに関して、CSA の担当者と協力してください。 ストレージの追加は 1 TB 単位です。

ボリュームについては、Basic レベルとエンタープライズ構成を使用して、Oracle の [Optimal Flexible Architecture (OFA) 標準](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)に従います。 標準の "T シャツ サイジング" 以外のカスタム ストレージ要件がある場合は、CSA を通じてカスタム要求を行います。

| 基本構成 (POC/テスト) | 説明 | Small | Medium | Large |
| --- | --- | --- | --- | --- |
| /u01 | Oracle バイナリ | 500 GB | 500 GB | 500 GB |
| /u02 | 読み取り集中型/管理者 | 500 GB | 1 TB (テラバイト) | 5 TB |
| /u03 | 書き込み集中型/ログ | 500 GB | 1 TB (テラバイト) | 5 TB |
| /u09 | バックアップ | 5 TB | 10 TB | 15 TB |

| エンタープライズ構成 | 説明 | Small | Medium | Large | Extra Large |
| --- | --- | --- | --- | --- | --- |
| /u01 | Oracle バイナリ | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | [Admin] | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 ～ /u59 | 読み取り集中型 | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 ～ /u89 | 書き込み集中型 | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 ～ /u91 | 再実行ログ | 500 GB | 500 GB | 1 TB (テラバイト) | 1 TB (テラバイト) |
| /u95 | アーカイブ | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | バックアップ | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>次のステップ

Oracle 用の BareMetal インフラストラクチャについて学びます。

> [!div class="nextstepaction"]
> [Azure での BareMetal インフラストラクチャとは](../../concepts-baremetal-infrastructure-overview.md)

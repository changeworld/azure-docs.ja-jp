---
title: Microsoft Azure Data Box のシステム要件 | Microsoft Docs
description: Azure Data Box のソフトウェア要件とネットワーキング要件について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 957100e042da1d41fb7c8fa27a5663cf99a41c20
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093937"
---
# <a name="azure-data-box-system-requirements"></a>Data Box のシステム要件

この記事では、Microsoft Azure Data Box と Data Box に接続するクライアントの重要なシステム要件について説明します。 この情報は、Data Box をデプロイする前によく確認し、デプロイ時やそれ以降の操作時にも、必要に応じて繰り返し参照することをお勧めします。

システム要件は次のとおりです。

* **Data Box に接続するホストのソフトウェア要件** - サポートされているプラットフォーム、ローカル Web UI 用のブラウザー、SMB クライアント、および Data Box に接続するホストのその他の要件についての情報を示します。
* **Data Box のネットワーク要件** - Data Box の最適な操作のためのネットワーク要件についての情報を示します。


## <a name="software-requirements"></a>ソフトウェア要件

ソフトウェア要件には、サポートされるオペレーティング システム、ローカル Web UI でサポートされるブラウザー、および SMB クライアントに関する情報が含まれます。

### <a name="supported-operating-systems-for-clients"></a>クライアントでサポートされるオペレーティング システム

Data Box デバイスに接続されたクライアント経由でデータのコピー操作を行う場合にサポートされるオペレーティング システムの一覧を次に示します。

| **オペレーティング システム** | **バージョン** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7、8、10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Linux クライアントでサポートされるファイル システム

| **プロトコル** | **バージョン** | 
| --- | --- | 
| SMB |2.X 以降 |
| NFS | 4.1 までのすべてのバージョン (4.1 も含まれます)|

### <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

Data Box デバイスでサポートされるストレージの種類の一覧を次に示します。

| **ストレージ アカウント** | **メモ** |
| --- | --- |
| クラシック | 標準 |
| 汎用  |標準。V1 と V2 の両方がサポートされます。 |
| BLOB |ホットとクールの両方がサポートされます。 |


### <a name="supported-storage-types"></a>サポートされるストレージの種類

Data Box デバイスでサポートされるストレージの種類の一覧を次に示します。

| **ファイル形式** | **メモ** |
| --- | --- |
| Azure ブロック BLOB | |
| Azure ページ BLOB  | データは 512 バイトでアラインされている必要があります。|
| Azure Files | |


### <a name="supported-web-browsers"></a>サポートされている Web ブラウザー

ローカル Web UI 用にサポートされている Web ブラウザーの一覧を次に示します。

| **ブラウザー** | **バージョン** | **その他の要件/注意事項** |
| --- | --- | --- |
| Google Chrome |最新バージョン |Chrome でテスト済み|
| Microsoft Edge |最新バージョン | |
| FireFox | 最新バージョン | FireFox でテスト済み|
| Internet Explorer |最新バージョン |サインインできない場合は、Cookie と Javascript が有効になっていることを確認します。 UI アクセスを有効にするには、デバイス IP を **[プライバシー操作]** に追加して、デバイスが Cookie にアクセスできるようにします。 |


## <a name="networking-requirements"></a>ネットワーク要件

お客様のデータセンターには、高速ネットワークが必要です。 少なくとも 1 本の 10 GbE 接続を利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用してデータをコピーできますが、コピーの速度が影響を受けます。

## <a name="next-step"></a>次のステップ

* [Azure Data Box をデプロイする](data-box-deploy-ordered.md)


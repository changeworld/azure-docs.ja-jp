---
title: SAP HANA on Azure (L インスタンス) での HANA のインストール方法 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) での HANA のインストール方法
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167648"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>HANA L インスタンスへの SAP HANA のインストールの例

このセクションでは、HANA L インスタンス ユニットに SAP HANA をインストールする方法について説明します。 開始時点の状態は次のとおりです。

- SAP HANA L インスタンスをデプロイするためのすべてのデータを Microsoft に提供済み。
- Microsoft から SAP HANA L インスタンスを入手済み。
- オンプレミス ネットワークに接続された Azure VNet を作成済み。
- HANA L インスタンスの ExpressRotue 回線を同じ Azure VNet に接続済み。
- HANA L インスタンスのジャンプ ボックスとして使用する Azure VM をインストール済み。
- ジャンプ ボックスから HANA L インスタンス ユニットへの接続とその逆方向の接続が可能であることを確認済み。
- 必要なパッケージとパッチがすべてインストールされているかどうかを確認済み。
- 使用している OS への HANA のインストールに関する SAP ノートとドキュメントを読み、選択した HANA リリースが OS リリースでサポートされていることを確認済み。

次の一連の手順では、ジャンプ ボックス VM (この例では Windows OS 上で実行) への HANA インストール パッケージのダウンロード、HANA L インスタンス ユニットへのパッケージのコピー、セットアップの手順について説明します。

## <a name="download-of-the-sap-hana-installation-bits"></a>SAP HANA インストール ビットのダウンロード
HANA L インスタンス ユニットはインターネットに直接接続していないため、インストール パッケージを SAP から HANA L インスタンス VM に直接ダウンロードすることはできません。 インターネットへの直接接続がないことに対処するには、ジャンプ ボックスが必要です。 パッケージをジャンプ ボックス VM にダウンロードします。

HANA インストール パッケージをダウンロードするには、SAP S-user または SAP Marketplace にアクセスできる他のユーザーである必要があります。 ログイン後、次の一連の画面に順に移動します。

[SAP Service Marketplace](https://support.sap.com/en/index.html) に移動し、[Download Software]、[Installations and Upgrade]、[By Alphabetical Index]、[Under H – SAP HANA Platform Edition]、[SAP HANA Platform Edition 2.0]、[Installation]、[Download] の順にクリックして、次のファイルをダウンロードします。

![HANA インストールのダウンロード](./media/hana-installation/image16_download_hana.PNG)

このデモでは、SAP HANA 2.0 インストール パッケージをダウンロードしました。 Azure ジャンプ ボックス VM で、次のように自己解凍形式のアーカイブをディレクトリに展開します。

![HANA インストールの抽出](./media/hana-installation/image17_extract_hana.PNG)

アーカイブを抽出したら、抽出によって作成されたディレクトリ (上記の例では 51052030) を、HANA L インスタンス ユニットの /hana/shared ボリュームに作成したディレクトリにコピーします。

> [!Important]
> インストール パッケージをルートまたはブート LUN にコピーしないでください。領域が限られており、他のプロセスも使用する必要があるためです。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA L インスタンス ユニットへの SAP HANA のインストール
SAP HANA をインストールするには、ルート ユーザーとしてログインする必要があります。 SAP HANA をインストールための十分なアクセス許可を持つのはルートだけです。
まず、/hana/shared にコピーしたディレクトリに対するアクセス許可を設定する必要があります。 アクセス許可は次のように設定する必要があります。

```
chmod –R 744 <Installation bits folder>
```

グラフィカル セットアップを使用して SAP HANA をインストールする場合は、HANA L インスタンスに gtk2 パッケージがインストールされている必要があります。 次のコマンドを使用して、このパッケージがインストールされているかどうかを確認します。

```
rpm –qa | grep gtk2
```

以降の手順では、グラフィカル ユーザー インターフェイスを使用して、SAP HANA のセットアップのデモを行っています。 次の手順として、インストール ディレクトリに移動し、サブディレクトリ HDB_LCM_LINUX_X86_64 に移動します。 開始

```
./hdblcmgui 
```
サブディレクトリから上記のコマンドを実行します。 これで、インストールに必要なデータを入力するための一連の画面が表示されます。 このデモでは、SAP HANA データベース サーバーと SAP HANA クライアント コンポーネントをインストールします。 したがって、次のように [SAP HANA Database] を選択します。

![インストールでの HANA の選択](./media/hana-installation/image18_hana_selection.PNG)

次の画面では、[Install New System] を選択します。

![HANA の新規インストールの選択](./media/hana-installation/image19_select_new.PNG)

この手順の後、SAP HANA データベース サーバーに追加でインストールできる複数の追加コンポーネントの中から選択する必要があります。

![HANA の追加コンポーネントの選択](./media/hana-installation/image20_select_components.PNG)

このドキュメントでは、SAP HANA Client と SAP HANA Studio を選択します。 また、スケールアップ インスタンスもインストールします。 そのため、次の画面で [Single-Host System] を選択する必要があります。 

![スケールアップ インストールの選択](./media/hana-installation/image21_single_host.PNG)

次の画面では、いくつかのデータを入力する必要があります。

![SAP HANA SID の入力](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA システム ID (SID) として、HANA L インスタンスのデプロイメントを注文したときに Microsoft に提供したものと同じ SID を入力する必要があります。 別の SID を選択すると、さまざまなボリュームでのアクセス許可の問題が原因でインストールが失敗します。

インストール ディレクトリとして、/hana/shared ディレクトリを使用します。 次に、HANA データ ファイルと HANA ログ ファイルの場所を指定する必要があります。


![HANA ログの場所の指定](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> この画面の前の選択画面で選択した SID を含むマウント ポイントが既に設定されているボリュームを、データ ファイルおよびログ ファイルとして定義します。 SID が前の画面で入力したものと一致しない場合は、前に戻り、SID をマウント ポイントに含まれている値に調整します。

次に、ホスト名を確認し、最終的な修正を行います。 

![ホスト名の確認](./media/hana-installation/image24_review_host_name.PNG)

次に、HANA L インスタンスのデプロイメントを注文したときに Microsoft に提供したデータも取得する必要があります。 

![システム ユーザーの UID と GID の入力](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> ユニットのデプロイメントを注文したときに Microsoft に提供したものと同じシステム ユーザー ID とユーザー グループの ID を入力する必要があります。 まったく同じ ID を入力しないと、HANA L インスタンス ユニットへの SAP HANA のインストールは失敗します。

このドキュメントには示されていませんが、次の 2 つの画面では、SAP HANA データベースの SYSTEM ユーザーのパスワードと、sapadm ユーザーのパスワードを入力する必要があります。sapadm ユーザーのパスワードは、SAP HANA データベース インスタンスの一部としてインストールされる SAP Host Agent で使用されます。

パスワードを定義すると確認画面が表示されます。 表示されているすべてのデータを確認し、インストールを続行します。 次のように、インストールの進行状況を示す進行状況画面が表示されます。

![インストールの進行状況の確認](./media/hana-installation/image27_show_progress.PNG)

インストールが終了すると、次のような画面が表示されます。

![インストールの終了](./media/hana-installation/image28_install_finished.PNG)

この時点で、SAP HANA インスタンスが起動して実行され、いつでも使用できる状態になります。 SAP HANA インスタンスには、SAP HANA Studio から接続することもできます。 また、SAP HANA の最新のパッチの有無を必ず確認し、それらのパッチを適用します。


**次のステップ**

- 「[Azure での SAP HANA L インスタンスの高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。


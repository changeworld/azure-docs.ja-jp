---
title: Azure HDInsight Ubuntu 18.04 更新プログラム
description: Azure HDInsight Ubuntu 18.04 OS の変更について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.author: yanacai
author: yanancai
ms.date: 05/25/2021
ms.openlocfilehash: 61d801d7091d2a619ff6a8b6436f386c125ca4be
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415123"
---
# <a name="hdinsight-ubuntu-1804-os-update"></a>HDInsight Ubuntu 18.04 OS 更新プログラム

この記事では、HDInsight Ubuntu 18.04 OS 更新プログラムと、必要になる可能性のある変更について詳しく説明します。

## <a name="update-overview"></a>更新プログラムの概要

HDInsight では、2021 年 5 月に、Ubuntu 18.04 で実行される新しい HDInsight 4.0 クラスター イメージのロールアウトを開始しました。 新しく作成された HDInsight 4.0 クラスターは、使用可能になった後、既定では Ubuntu 18.04 で実行されます。 Ubuntu 16.04 上の既存のクラスターは、完全サポートでそのまま実行されます。

HDInsight 3.6 は、Ubuntu 16.04 で引き続き実行されます。 2021 年 6 月 30 日で標準サポートが終了し、2021 年 7 月 1 日から Basic サポートに変更されます。 日付とサポート オプションの詳細については、「[Azure HDInsight のバージョン](./hdinsight-component-versioning.md)」を参照してください。 Ubuntu 18.04 は、HDInsight 3.6 ではサポートされません。 Ubuntu 18.04 を使用する場合は、クラスターを HDInsight 4.0 に移行する必要があります。

既存のクラスターを Ubuntu 18.04 に移行する場合は、クラスターを削除して再作成します。 クラスターの作成または再作成を計画してください。 

## <a name="script-actions-changes"></a>スクリプト アクションの変更

HDInsight のスクリプト アクションは、追加のコンポーネントをインストールし、構成設定を変更するために使用されます。 スクリプト アクションは、HDInsight クラスター内のノードで実行される Bash スクリプトです。

スクリプト アクションに対して、いくつかの変更を加えることが必要になる可能性があります。

**必要に応じてパッケージを取得するときに、`xenial` の各インスタンスを `bionic` に変更する**:

次に例を示します。
- `http://packages.treasuredata.com/3/ubuntu/xenial/ xenial contrib` を `http://packages.treasuredata.com/3/ubuntu/bionic/ bionic contrib` に更新します。
- `http://azure.archive.ubuntu.com/ubuntu/ xenial main restricted` を `http://azure.archive.ubuntu.com/ubuntu/ bionic main restricted` に更新します。

**bionic には一部のパッケージ バージョンが存在しない:** 

たとえば、[Node.js バージョン 4.x](https://deb.nodesource.com/node_4.x/dists/) は bionic リポジトリには存在しません。 [Node.js バージョン 12.x](https://deb.nodesource.com/node_12.x/dists/bionic/) が存在します。

bionic に存在しない古いバージョンをインストールするスクリプトは、新しいバージョンに更新する必要があります。

**18.04 には /etc/rc.local は既定で存在しない:**

一部のスクリプトでは、サービスのスタートアップに `/etc/rc.local` を使用しますが、これは Ubuntu 18.04 には既定で存在しません。 適切な systemd ユニットに変換する必要があります。 

**基本 OS パッケージが更新されている:**

スクリプトが Ubuntu 16.04 の古いバージョンのパッケージに依存している場合、動作しない可能性があります。 クラスター ノードに SSH 接続し、クラスタ ノードで `dpkg --list` を実行すると、インストールされているすべてのパッケージの詳細が表示されます。
 
**一般に、Ubuntu 18.04 には 16.04 よりも厳しい規則があります。**

## <a name="custom-applications"></a>カスタム アプリケーション
一部の[サード パーティ アプリケーション](./hdinsight-apps-install-applications.md)は、HDInsight クラスターにインストールできます。 これらのアプリケーションは、Ubuntu 18.04 では適切に機能しない可能性があります。 破壊的変更のリスクを軽減するために、HDInsight では、2021 年 2 月 25 日以降にカスタム アプリケーションがインストールされたサブスクリプションには新しいイメージをロールアウトしません。 テスト サブスクリプションで新しいイメージを試す場合は、サポート チケットを開いてサブスクリプションを有効にしてください。

## <a name="edge-nodes"></a>エッジ ノード
新しいイメージを使用すると、クラスターのエッジノードの OS も Ubuntu 18.04 に更新されます。 既存のクライアントは、Ubuntu 18.04 でテストする必要があります。 破壊的変更のリスクを軽減するために、HDInsight では、2021 年 2 月 25 日以降にエッジ ノードを使用していたサブスクリプションには新しいイメージをロールアウトしません。 テスト サブスクリプションで新しいイメージを試す場合は、サポート チケットを開いてサブスクリプションを有効にしてください。

## <a name="references"></a>リファレンス
 - [Ubuntu 18.04 LTS のリリース ノート](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes/)






---
title: "Azure Cloud Shell (プレビュー) の概要 | Microsoft Docs"
description: "Azure Cloud Shell について概説します。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 47627bc6df93db1d92aa29350fe6e48039dc6f1b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure Cloud Shell (プレビュー) の概要
Azure Cloud Shell は、Azure リソースを管理するための、ブラウザーでアクセスできるインタラクティブなシェルです。

![](media/startup.gif)

## <a name="features"></a>Features (機能)
### <a name="browser-based-shell-experience"></a>ブラウザーベースのシェル環境
Cloud Shell は、Azure の管理タスクを視野に入れて開発されたブラウザーベースのコマンド ライン環境です。 Cloud Shell を利用することで、ローカル コンピューターに拘束されることなくクラウドならではの作業が可能となります。

### <a name="pre-configured-azure-workstation"></a>事前構成済みの Azure ワークステーション
Cloud Shell は、作業のスピードを高めるために、広く使われているコマンド ライン ツールや言語サポートと共にプレインストールされます。

[Azure Cloud Shell に用意されている詳細なツールの一覧はこちら](features.md#tools)からご覧いただけます。

### <a name="automatic-authentication"></a>自動認証
Azure CLI 2.0 ですばやくリソースにアクセスできるよう、Cloud Shell では各セッションで安全に自動認証が行われます。

### <a name="connect-your-azure-file-storage"></a>Azure File Storage の接続
Cloud Shell マシンは一時的なものであるため、お使いの $Home ディレクトリを永続化するためには、Azure ファイル共有をマウントする必要があります。
Cloud Shell の初回起動時に、リソース グループとストレージ アカウント、ファイル共有を自動的に作成するよう促されます。 これは 1 回限りの作業であり、それ以降はすべてのセッションで自動的に接続されます。 

![](media/storage-prompt.png)

既定の 5 GB のディスク イメージを含んだ Azure ファイル共有と共に、LRS ストレージ アカウントが自動的に作成されます。
このディスク イメージを使って $Home ディレクトリが同期され、永続化されます。 ストレージのコストは通常どおりに適用されます。
以下の 3 つのリソースが自動的に作成されます。
1. `cloud-shell-storage-<region>` という名前のリソース グループ
2. `cs-uniqueGuid` という名前のストレージ アカウント
3. `cs-<user>-<domain>-com-uniqueGuid` という名前のファイル共有

[Cloud Shell におけるファイルの永続化について詳しくは、こちらを参照してください](persisting-shell-storage.md)。

## <a name="concepts"></a>概念
* Cloud Shell は、ユーザーごとにセッション単位で一時的に提供されるマシン上で実行されます。
* Cloud Shell は、無操作状態で 10 分経過するとタイムアウトとなります。
* Cloud Shell は、ファイル共有を接続した状態でのみアクセスできます。
* Cloud Shell には、ユーザー アカウントごとに 1 台のマシンが割り当てられます。
* アクセス許可は、標準の Linux ユーザーとして設定されます。

[Cloud Shell の全機能については、こちらを参照してください。](features.md)

## <a name="examples"></a>例
* 任意のブラウザーから Azure リソースを管理するためのスクリプトを作成または編集する。
* Azure Portal と Azure CLI 2.0 を同時に使ってリソースを管理する。
* Azure CLI 2.0 を試用する。

[以上の例はすべて Cloud Shell クイック スタートでお試しいただけます。](quickstart.md)

## <a name="pricing"></a>価格
Cloud Shell のホストとなるマシンは無料です。ただし $Home ディレクトリを永続化するために、前提条件として Azure ファイル共有をマウントする必要があります。 ストレージのコストは通常どおりに適用されます。

## <a name="supported-browsers"></a>サポートされているブラウザー
Cloud Shell は、Chrome、Edge、Safari での使用をお勧めします。 Cloud Shell は Chrome、Firefox、Safari、IE、Edge でサポートされますが、ブラウザー固有の設定の影響を受けます。
具体的に確認されている制限については、[Cloud Shell の制限](limitations.md)に関するページを参照してください。

---
title: Azure Data Studio ダッシュボード
description: Azure Data Studio ダッシュボード
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: cea97bab303ce2d009cecc67ed30ec89b0992118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554338"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio ダッシュボード

[Azure Data Studio](/sql/azure-data-studio/what-is) では、Azure Arc リソースに関する情報を表示するために、Azure portal と同様のエクスペリエンスが提供されます。  これらのビューは **ダッシュボード** と呼ばれ、レイアウトとオプションは、Azure portal 内の特定のリソースについて見られるものと似ていますが、Azure に接続できない場合に環境内の情報がローカルに表示される柔軟性があります。


## <a name="connecting-to-a-data-controller"></a>データ コントローラーに接続する

### <a name="prerequisites"></a>前提条件

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) をダウンロードする
- Azure Arc 拡張機能がインストールされている



### <a name="connect"></a>接続する

1. Azure Data Studio を開きます
2. 左側にある **[接続]** タブを選択します。
3. **[Azure Arc コントローラー]** というパネルを展開します
4. **[コントローラーの接続]** ボタンをクリックします。 これにより、ブレードが右側に表示されます
5. 既定では、Azure Data Studio によって既定のディレクトリ内の kube.config ファイルからの読み取りが試行され、使用可能な kubernetes クラスター コンテキストの一覧が表示されて現在のクラスター コンテキストが自動で選択されます。 このクラスターが正しい接続先の場合は、 **[名前空間]** の入力に Azure Arc データ コントローラーが展開されている名前空間を入力します。 Azure Arc データ コントローラーが展開されている名前空間を取得する必要がある場合は、kubernetes クラスターで ```kubectl get datacontrollers -A``` を実行します。 
6. 必要に応じて、 **[名前]** の入力に Azure Arc データ コントローラーの表示名を追加します
7. **[接続]** を選択します


データ コントローラーに接続されたら、データ コントローラーのダッシュボードと、使用している任意の SQL Managed Instance または PostgreSQL Hyperscale サーバー グループ リソースを表示できます。

## <a name="view-the-data-controller-dashboard"></a>データ コントローラー ダッシュボードを表示する

**[Arc コントローラー]** という展開可能なパネルの [接続] パネルでデータ コントローラーを右クリックし、 **[管理]** を選択します。

ここには、データ コントローラー リソースに関する詳細 (名前、地域、接続モード、リソース グループ、サブスクリプション、コントローラー エンドポイント、名前空間など) を表示できます。  データ コントローラーで管理されているすべてのマネージド データベース リソースのリストも表示できます。

レイアウトは、Azure portal で見られるものと似ていることがわかります。

[+ 新しいインスタンス] ボタンをクリックすれば、簡単に SQL Managed Instance または PostgreSQL Hyperscale サーバー グループの作成を開始できます。

また、[Azure Portal で開きます] ボタンをクリックして、このデータ コントローラーのコンテキストで Azure portal を開くこともできます。

## <a name="view-the-sql-managed-instance-dashboards"></a>SQL Managed Instance ダッシュボードを表示する

いくつかの SQL Managed Instance を作成した場合は、管理されているデータ コントローラーの下にある [Azure データ コントローラー] という展開可能なパネルの [接続] パネルに一覧が表示されているのを確認できます。

特定のインスタンスの SQL Managed Instance ダッシュボードを表示するには、そのインスタンスを右クリックし、[管理] を選択します。

右側に [接続] パネルがポップアップ表示され、その SQL インスタンスに接続するためのログインとパスワードを入力するように求められます。 接続情報がわかっている場合は、それを入力し、[接続] をクリックできます。  わからない場合は、[キャンセル] をクリックできます。  どちらの方法でも、[接続] パネルを閉じるとダッシュボードが表示されます。

[概要] タブには、SQL Managed Instance に関する詳細 (リソース グループ、データ コントローラー、サブスクリプション ID、状態、リージョンなど) を表示できます。  また、クリックすれば、その SQL Managed Instance のコンテキストで Grafana ダッシュボードまたは Kibana ダッシュボードに移動できるリンクも表示できます。

SQL Managed Instance に接続できる場合は、ここに追加の情報を表示できます。

ここから SQL Managed Instance を削除することも、Azure portal を開いて、Azure portal 内の SQL Managed Instance を表示することもできます。

左側にある [接続文字列] タブをクリックすると、その SQL Managed Instance で事前に作成された接続文字列のリストを表示できます。そのため、簡単に文字列をコピーして他のさまざまなアプリケーションやコードに貼り付けることができます。

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>PostgreSQL Hyperscale サーバー グループ ダッシュボードを表示する

いくつかの PostgreSQL Hyperscale サーバー グループを作成した場合は、管理されているデータ コントローラーの下にある [Azure データ コントローラー] という展開可能なパネルの [接続] パネルに一覧が表示されているのを確認できます。

特定のサーバー グループの PostgreSQL Hyperscale サーバー グループ ダッシュボードを表示するには、そのサーバー グループを右クリックし、[管理] を選択します。

[概要] タブには、サーバー グループに関する詳細 (リソース グループ、データ コントローラー、サブスクリプション ID、状態、リージョンなど) を表示できます。  また、クリックすれば、そのサーバー グループのコンテキストで Grafana ダッシュボードまたは Kibana ダッシュボードに移動できるリンクも表示できます。

ここからサーバー グループを削除することも、Azure portal を開いて、Azure portal 内のサーバー グループを表示することもできます。

左側にある [接続文字列] タブをクリックすると、そのサーバー グループで事前に作成された接続文字列のリストを表示できます。そのため、簡単に文字列をコピーして他のさまざまなアプリケーションやコードに貼り付けることができます。

左側にある [プロパティ] タブをクリックすると、追加の詳細を表示できます。

左側にある [リソース正常性] タブをクリックすると、そのサーバー グループの現在の高度な正常性を表示できます。

左側にある [問題の診断と解決] タブをクリックすると、PostgreSQL のトラブルシューティング ノートブックを起動できます。

左側にある [新しいサポート要求] タブをクリックすると、サーバー グループのコンテキストで Azure portal を起動し、そこから Azure サポート要求を作成できます。
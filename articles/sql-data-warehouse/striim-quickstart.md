---
title: Azure SQL Data Warehouse での Striim クイック スタート | Microsoft Docs
description: Striim と Azure SQL Data Warehouse の使用をすぐに開始します。
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 09f3126725b9a2cd6a8277447c0eed4a16838429
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355364"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim の Azure SQL DW の Marketplace サービスのインストール ガイド

このクイックスタートでは、SQL Data Warehouse の既存のインスタンスが既にあることを前提とします。

Azure Marketplace で Striim を検索し、[Striim for Data Integration to SQL Data Warehouse (Staged)]\(SQL Data Warehouse へのデータ統合用の Striim (ステージング)) オプションを選択します 

![Striim をインストールする][install]

Striim クラスター名、パスワード、および管理者のパスワードを書き留めて、指定したプロパティで Striim VM を構成します

![Striim を構成する][configure]

デプロイした後、Azure portal で <VM Name>-masternode をクリックし、[接続] をクリックし、[Login using VM local account]\(VM ローカル アカウントを使用してログイン) をコピーします 

![Striim を SQL Data Warehouse に接続する][connect]

sqljdbc42.jar を <https://www.microsoft.com/en-us/download/details.aspx?id=54671> からローカル コンピューターにダウンロードします。 

コマンドライン ウィンドウを開き、JDBC jar をダウンロードした場所にディレクトリを変更します。 SCP で jar ファイルを Striim VM にコピーして、Azure portal からのアドレスとパスワードを取得します

![jar ファイルを VM にコピーする][copy-jar]

別のコマンド ライン ウィンドウを開くか、ssh ユーティリティを Striim クラスターに ssh 接続します

![クラスターに SSH 接続する][ssh]

次のコマンドを実行して、JDBC jar ファイルを Striim の lib ディレクトリに移動し、サーバーを起動および停止します。

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Striim クラスターを起動する][start-striim]

ここで、お好きなブラウザーを開いて、<DNS Name>:9080 に移動します

![ログイン画面に移動する][navigate]

Azure portal で設定したユーザー名とパスワードでログインし、適切なウィザードを選択して作業を開始するか、アプリのページに移動してドラッグ アンド ドロップ UI の使用を開始します

![サーバーの資格情報でログインします][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
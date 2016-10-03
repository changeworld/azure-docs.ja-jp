<properties
	pageTitle="Hadoop サンドボックスを使用して Hadoop について学ぶ| Microsoft Azure"
	description="Hadoop エコシステムの使用について学ぶには、まず Hortonworks の Hadoop サンドボックスを Azure 仮想マシンに設定します。"
	keywords="hadoop emulator,hadoop sandbox"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# 仮想マシンの Hadoop サンドボックスを使用した Hadoop エコシステム入門

Hortonworks の Hadoop サンドボックスを仮想マシンにインストールする方法と、Hadoop エコシステムについて学ぶことができます。Hadoop、Hadoop 分散ファイル システム (HDFS)、ジョブの送信などについて理解できるように、サンドボックスにはローカル開発環境が用意されています。

## 前提条件

* [Oracle VirtualBox](https://www.virtualbox.org/)

Hadoop について理解できたら、HDInsight クラスターを作成して、Azure で Hadoop を使い始めることができます。使用方法の詳細については、[HDInsight での Hadoop の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

## 仮想マシンのダウンロードとインストール

1. [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox) で、HDP 2.4 on Hortonworks Sandbox 用の __[DOWNLOAD FOR VIRTUALBOX (VirtualBox のダウンロード)]__ 項目を選択します。ダウンロードが始まる前に、Hortonworks に登録するように求められます。

    ![Link image for download Hortonworks Sandbox for VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. 同じ Web ページで、HDP 2.4 on Hortonworks Sandbox 用の __[VirtualBox Install Guide (VirtualBox インストール ガイド)]__ を選択します。仮想マシンのインストール手順が記載された PDF がダウンロードされます。

    ![View the install guide](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## 仮想マシンの開始

1. VirtualBox を開始して、Hortonworks Sandbox を選択し、__[Start (開始)]__、__[Normal Start (通常の方法で開始)]__ の順にクリックします。

    ![Normal start](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. 仮想マシンのブート プロセスが終了すると、ログインに関する手順が表示されます。Web ブラウザーを開き、表示された URL に移動します (通常は http://127.0.0.1:8888)。

## パスワードの設定

1. Hortonworks Sandbox のページの __[get started (開始)]__ 手順で、__[View Advanced Options (詳細設定の表示)]__ をクリックします。SSH を使用してサンドボックスにログインするには、このページの情報を使用します。提供された名前とパスワードを使用してください。

    > [AZURE.NOTE] SSH クライアントがインストールされていない場合は、\_\_http://localhost:4200/__ の仮想マシンから提供される Web ベースの SSH を使用することができます。

    SSH を使用して初めて接続を行う際、ルート アカウントのパスワードを変更するように求められます。将来、SSH を使用してログインするときに使用する新しいパスワードを入力します。

2. ログインが完了したら、次のコマンドを入力します。

        ambari-admin-password-reset
    
    メッセージが表示されたら、Ambari の管理者アカウントのパスワードを入力します。これは、Ambari Web UI にアクセスするときに使用されます。

## Hive コマンドの使用

1. サンドボックスへの SSH 接続から、次のコマンドを使用して Hive シェルを起動します。

        hive

2. シェルが起動したら、次のコマンドを使用して、サンドボックスに用意されているテーブルを表示します。

        show tables;

3. 次のコマンドを使用して、`sample_07` テーブルから 10 行を取得します。

        select * from sample_07 limit 10;

## 次のステップ

* [Hortonworks Sandbox で Visual Studio を使用する方法](hdinsight-hadoop-emulator-visual-studio.md)
* [Hortonworks Sandbox の使い方のヒント](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop チュートリアル: HDP の概要](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->
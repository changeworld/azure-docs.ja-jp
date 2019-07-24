---
title: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する - Azure
description: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503245"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight 用の OS の修正プログラム 

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に入手できるようになり、新しい HDInsight クラスターの作成に使用できます。 2019 年 1 月時点で、実行中のクラスターに修正プログラムは自動適用**されません**。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法
HDInsight クラスターの仮想マシンは、重要なセキュリティ更新プログラムをインストールできるように、ときどき再起動する必要があります。 

この記事で説明するスクリプト アクションを使用して、次のように OS 修正プログラム適用スケジュールを変更することができます。
1. すべての更新プログラムをインストールする、カーネル + セキュリティ更新プログラムのみをインストールする、またはカーネル更新プログラムのみをインストールする。
2. VM を即座に再起動する、または VM の再起動をスケジュールする。

> [!NOTE]  
> このスクリプト アクションは、2016 年 8 月 1 日以降に作成された Linux ベースの HDInsight クラスターでのみ機能します。 修正プログラムは、VM が再起動された場合のみ有効になります。 これらのスクリプトでは、将来のすべての更新サイクルで更新プログラムが自動的に適用されることはありません。 更新プログラムをインストールして VM を再起動するには、新しい更新プログラムを適用する必要があるたびに、スクリプトを実行してください。

## <a name="how-to-use-the-script"></a>スクリプトの使用方法 

このスクリプトを使用するには、次の情報が必要です。
1. install-updates-schedule-reboots スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight では、この URI を使用してクラスター内のすべての仮想マシンでスクリプトを検索して実行します。 このスクリプトは、更新プログラムをインストールして VM を再起動するためのオプションを提供します。
  
2. schedule-reboots スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight では、この URI を使用してクラスター内のすべての仮想マシンでスクリプトを検索して実行します。 このスクリプトは VM を再起動します。
  
3. スクリプトが適用されるクラスター ノードの種類: headnode、workernode、zookeeper。 このスクリプトは、クラスター内のすべてのノードの種類に適用する必要があります。 ノードの種類に適用しないと、そのノードの種類の仮想マシンは更新されることも再起動されることもありません。

4. パラメーター:install-updates-schedule-reboots スクリプトでは、2 つの数値パラメーターが受け付けられます。

    | パラメーター | 定義 |
    | --- | --- |
    | カーネル更新プログラムのみをインストールする/すべての更新プログラムをインストールする/カーネル + セキュリティ更新プログラムのみをインストールする |0 または 1 または 2。 値 0 はカーネル更新プログラムのみをインストールします。値 1 はすべての更新プログラムをインストールし、値 2 はカーネル + セキュリティ更新プログラムのみをインストールします。 パラメーターを指定しない場合、既定値は 0 です。 |
    | 再起動なし/スケジュール再起動を有効にする/即時再起動を有効にする |0 または 1 または 2。 値 0 は再起動を無効にします。値 1 はスケジュール再起動を有効にし、値 2 は即時再起動を有効にします。 パラメーターを指定しない場合、既定値は 0 です。 ユーザーは、入力パラメーター 1 を入力パラメーター 2 にする必要があります。 |
   
 5. パラメーター:schedule-reboots スクリプトでは、1 つの数値パラメーターが受け付けられます。

    | パラメーター | 定義 |
    | --- | --- |
    | スケジュール再起動を有効にする/即時再起動を有効にする |1 または 2。 値 1 はスケジュール再起動を有効にします (再起動は、12 - 24 時間後にスケジュールされます)。値 2 は即時再起動を有効にします (5 分以内)。 パラメーターを指定しない場合、既定値は 1 です。 |  

> [!NOTE] 
> 既存のクラスターに適用する場合、このスクリプトは保存済みとしてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。  クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に永続化されます。


## <a name="next-steps"></a>次の手順

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションをご覧ください。

* [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

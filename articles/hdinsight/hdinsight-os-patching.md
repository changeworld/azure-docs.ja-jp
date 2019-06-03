---
title: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する - Azure
description: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: cfbd68e66730fc338130bc16849fe0b2f4abd6be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244404"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight 用の OS の修正プログラム 

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に、新しい HDInsight クラスターの作成のために入手できるようになります。 2019 年 1 月時点で、実行中のクラスターに修正プログラムは自動適用**されません**。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法
HDInsight クラスターの仮想マシンは、重要なセキュリティ更新プログラムをインストールできるように、ときどき再起動する必要があります。 

この記事で説明するスクリプト アクションを使用して、次のように OS の修正プログラム適用スケジュールを変更することができます。
1. OS の完全な更新プログラムをインストールする、またはセキュリティ更新プログラムのみをインストールする
2. VM を再起動する

> [!NOTE]  
> このスクリプト アクションは、2016 年 8 月 1 日以降に作成された Linux ベースの HDInsight クラスターでのみ機能します。 修正プログラムは、VM が再起動された場合のみ有効になります。 このスクリプトでは、将来のすべての更新サイクルで更新プログラムが自動的に適用されることはありません。 更新プログラムをインストールして VM を再起動するには、新しい更新プログラムを適用する必要があるたびに、スクリプトを実します。

## <a name="how-to-use-the-script"></a>スクリプトの使用方法 

このスクリプトを使用するには、次の情報が必要です。
1. スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh。HDInsight では、この URI を使用してクラスター内のすべての仮想マシンでスクリプトを検索して実行します。
  
2. スクリプトが適用されるクラスター ノードの種類: headnode、workernode、zookeeper。 このスクリプトは、クラスター内のすべてのノードの種類に適用する必要があります。 ノードの種類に適用しないと、そのノードの種類の仮想マシンは更新されません。


3.  パラメーター:このスクリプトでは、1 つの数値パラメーターが受け付けられます。

    | パラメーター | 定義 |
    | --- | --- |
    | OS の完全な更新プログラムをインストールする/セキュリティ更新プログラムのみをインストールする |0 または 1。 値を 0 にすると、セキュリティ更新プログラムのみがインストールされます。1 にすると、OS の完全な更新プログラムがインストールされます。 パラメーターを指定しない場合の既定値は 0 です。 |

> [!NOTE]  
> 既存のクラスターに適用する場合、このスクリプトは永続化としてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。  クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に永続化されます。


## <a name="next-steps"></a>次の手順

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションをご覧ください。

* [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

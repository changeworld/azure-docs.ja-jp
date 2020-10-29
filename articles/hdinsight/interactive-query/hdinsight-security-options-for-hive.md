---
title: Azure HDInsight の Hive のセキュリティ オプション
description: 標準および ESP クラスターの Hive のセキュリティ オプション。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 8573ba99b7aef13025b4f175640ac9583ad5a679
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545958"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight の Hive のセキュリティ オプション

このドキュメントでは、HDInsight の Hive で推奨されるセキュリティ オプションについて説明します。 これらのオプションは、Ambari を使用して構成できます。

![Hive のセキュリティ オプション](./media/hdinsight-security-options-for-hive/security-options-hive.png "Hive のセキュリティ オプション")

## <a name="hiveserver2-authentication"></a>HiveServer2 認証

標準クラスターの場合、HiveServer2 認証で推奨される設定は既定値の [なし] です。 認証を有効にするには、[ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise セキュリティ パッケージ) クラスターにアップグレードすることが推奨されています。 

ESP クラスターの場合、規定で [Kerberos](https://web.mit.edu/Kerberos/) 認証が有効になっています。 Pluggable Authentication Module (PAM) とカスタム認証スキームはサポートされていません。

## <a name="hiveserver2-authorization"></a>HiveServer2 認可

標準クラスターの場合、既定の設定は [なし] です。 [SqlStdAuth (SQL Standards Based Authorization)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) を有効にすることができます。 [Apache Ranger](https://ranger.apache.org/) を使用した認可は、標準クラスターでサポートされていません。 Ranger 認可を行うために、ESP クラスターにアップグレードすることが推奨されています。 

ESP クラスターの場合、既定で Ranger を使用した認可が有効になっています。 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2 の SSL 暗号化

標準クラスターまたは ESP クラスターの場合、Hiveserver2 SSL を有効にすることが推奨されていません。 代わりに、SSL がゲートウェイで有効になっています。 [転送中の暗号化](../domain-joined/encryption-in-transit.md)を有効にすると、[インターネット プロトコル セキュリティ (IPSec)](https://en.wikipedia.org/wiki/IPsec) を使用してクラスター ノード間の通信を暗号化できます。


## <a name="next-steps"></a>次の手順
* [HiveServer2 認証の概要](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 認可の概要](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [SQL 標準ベースの Hive 認可を有効にする](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Hive を使用した Apache Ranger](../domain-joined/apache-domain-joined-run-hive.md)

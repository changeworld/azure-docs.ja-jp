---
title: Azure HDInsight で Zeppelin にサインインできない
description: Azure HDInsight で Zeppelin にサインインできない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896147"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>シナリオ:Azure HDInsight で Apache Zeppelin にサインインできない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Active Directory の ADDS パスワードを変更した後、Apache Zeppelin にサインインできなくなりました。

## <a name="cause"></a>原因

`shiro_ini` ファイルの `activeDirectoryRealm.systemUsername` に指定されているユーザーが、Active Directory のパスワードを変更しました。

## <a name="resolution"></a>解決策

1. Ambari の Zeppelin `activeDirectoryRealm.systemPassword = <new password>` config に `shiro_ini` を追加して、変更されたパスワードが根本原因であることを確認します。 `activeDirectoryRealm.hadoopSecurityCredentialPath` 設定を削除します。 `shiro ini` の場所は次のとおりです。

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. 手順 1 の後にユーザーが Zeppelin にサインインできるようになった場合は、新しいパスワードを使用して新しい `jceks` ファイルを作成し、`activeDirectoryRealm.hadoopSecurityCredentialPath` を新しいファイルに置き換えます。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。

---
title: アクセスされているアカウントでは Azure HDInsight の http エラーをサポートしていない
description: この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163832"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>アクセスされているアカウントでは Azure HDInsight の http エラーをサポートしていない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

次のエラー メッセージを受け取ります。

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

このエラー メッセージを受け取る理由は複数あります。

* ストレージ アカウントで[セキュリティで保護された転送](../../storage/common/storage-require-secure-transfer.md)が有効であり、誤った [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)が使用されています。

* セキュリティで保護された転送が "*無効*" なストレージ アカウントでクラスターが作成されました。 その後、ストレージ アカウントでセキュリティで保護された転送が有効になりました。

## <a name="resolution"></a>解決策

Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ `wasbs://` または `abfss://` になります。  [安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

新しいクラスターの場合は、必要なセキュリティで保護された転送設定が既にあるストレージ アカウントを使用します。 既存のクラスターで使用中のストレージ アカウントの、セキュリティで保護された転送設定は変更しないでください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。

---
title: DocumentDB アカウント クォータの引き上げ要求 | Microsoft Docs
description: コレクションあたりのドキュメント ストレージやスループットなどの、DocumentDB データベース のクォータの調整を要求する方法について説明します。
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: anhoh

---
# DocumentDB アカウント制限の引き上げを要求する
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) には、Azure サポートに問い合わせて調整できる既定のクォータのセットがあります。この記事では、クォータの引き上げを要求する方法について説明します。

この記事を読むと、次の質問に回答できるようになります。

* Azure サポートに問い合わせて調整できるのは、どの DocumentDB データベース クォータか。
* DocumentDB アカウント クォータの調整を要求するにはどうすればよいか。

## <a id="Quotas"></a> DocumentDB アカウント クォータ
次の表で、DocumentDB クォータについて説明します。アスタリスク (*) が付いているクォータを調整する場合は、Azure サポートまでお問い合わせください。

[!INCLUDE [azure-documentdb-limits](../../includes/azure-documentdb-limits.md)]

## <a id="RequestQuotaIncrease"></a> クォータ調整の要求
クォータ調整を要求するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)の **[More Services (その他のサービス)]**、**[ヘルプとサポート]** を順にクリックします。
   
    ![ヘルプとサポート起動時のスクリーンショット](media/documentdb-increase-limits/helpsupport.png)
2. **[ヘルプとサポート]** ブレードで **[新しいサポート要求]** をクリックします。
   
    ![サポート チケット作成のスクリーンショット](media/documentdb-increase-limits/getsupport.png)
3. **[新しいサポート要求]** ブレードで、**[基本]** をクリックします。次に、**[問題の種類]** を **[クォータ]** に、**[サブスクリプション]** を自分の DocumentDB アカウントをホストするサブスクリプションに、**[クォータの種類]** を **[DocumentDB]** に、**[サポート プラン]** を **[Quota SUPPORT - Included (クォータ サポート - 含む)]** に設定します。その後、**[次へ]** をクリックします。
   
    ![サポート チケットのリクエストの種類のスクリーンショット](media/documentdb-increase-limits/supportrequest1.png)
4. **[問題]** ブレードで重大度を選択し、クォータの増大に関する情報を **[詳細]** に入力します。**[次へ]** をクリックします。
   
    ![サポート チケットのサブスクリプション選択のスクリーンショット](media/documentdb-increase-limits/supportrequest2.png)
5. 最後に、連絡先情報を **[連絡先情報]** ブレードに入力し、**[作成]** をクリックします。

サポート チケットが作成されると、サポート要求番号が電子メールで届きます。サポート要求は、**[ヘルプとサポート]** ブレードの **[サポート要求]** をクリックして表示することもできます。

![サポート リクエスト ブレードのスクリーンショット](media/documentdb-increase-limits/supportrequest4.png)

## <a name="NextSteps"></a> 次のステップ
* DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。

<!---HONumber=AcomDC_0831_2016-->
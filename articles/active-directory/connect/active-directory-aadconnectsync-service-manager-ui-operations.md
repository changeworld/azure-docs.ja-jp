---
title: "Azure AD Connect 同期: Synchronization Service Manager UI | Microsoft Docs"
description: "Azure AD Connect の Synchronization Service Manager の [操作] タブについて"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 610dab0af17f927d86b677f647acd0dfe2569583
ms.openlocfilehash: 53b98aaf67b874b0af7d0e94e29bcbe23fc6fc5b


---
# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure AD Connect 同期: Synchronization Service Manager

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

[操作] タブには、最近の操作の結果が表示されます。 このタブは問題を理解し、解決するための鍵となります。

## <a name="understand-the-information-visible-in-the-operations-tab"></a>[操作] タブに表示される情報を理解する
上半分にはすべての実行が時系列で表示されます。 既定では、操作ログには過去 7 日間の情報が保持されますが、この設定は [スケジューラ](active-directory-aadconnectsync-feature-scheduler.md)で変更できます。 状態が成功になっていない実行を探したい場合があります。 見出しをクリックすると、並べ替えを変更できます。

**[Status]** (ステータス) 列は最も重要な情報であり、実行関連で最も深刻な問題を示します。 最も一般的なステータスを調査の優先度に基づいて簡単にまとめると次のようになります (* はエラー文字列が入ることを意味します)。

| [Status] | コメント |
| --- | --- |
| stopped-* |実行を完了できませんでした。 たとえば、リモート システムがダウンし、連絡できない場合などです。 |
| stopped-error-limit |エラーの数が 5,000 を超えています。 大量のエラーに起因し、実行が自動的に停止しました。 |
| completed-\*-errors |実行は完了しましたが、エラーが発生し (5,000 件未満)、調査が必要です。 |
| completed-\*-warnings |実行は完了しましたが、一部のデータが予想と異なります。 エラーがある場合には通常、このメッセージは単なる症状の 1 つにすぎません。 エラーを解決するまでは、警告を調査しないでください。 |
| 成功 |問題ありません。 |

行を選択すると、下の領域が更新され、実行の詳細が表示されます。 下の領域の左端には、 **Step #**(# は番号) という一覧が表示されることがあります。 これは、フォレストに複数のドメインがあり、手順が各ドメインを表す場合にのみ表示されます。 ドメイン名は **[Partition]**(パーティション) という見出しにあります。 **[Synchronization Statistics (同期統計)]**には、処理された変更の数に関する詳細が表示されます。 リンクをクリックすると、変更されたオブジェクトの一覧を取得できます。 オブジェクトにエラーがある場合、 **[Synchronization Errors (同期エラー)]**の下に表示されます。

## <a name="troubleshoot-errors-in-operations-tab"></a>[オペレーション] タブでエラーを解決する
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
エラーがある場合、エラーのあるオブジェクトとエラー自体がリンクとなり、クリックすると詳細が表示されます。

まず、エラー文字列をクリックしてみます (上の画像の**sync-rule-error-function-triggered** )。 最初にオブジェクトの概要が表示されます。 実際のエラーを確認するには、 **[スタック トレース]**ボタンをクリックします。 このトレースにより、エラーのデバッグ レベル情報が表示されます。

**ヒント:** **[call stack information (コール スタック情報)]** ボックスで右クリックし、**[select all (すべて選択)]** を選択して、**[copy (コピー)]** を選択します。 次に、スタックをコピーし、メモ帳など、普段利用しているエディターでエラーを確認できます。

* エラー元が **SyncRulesEngine**の場合、コール スタック情報の最初にオブジェクトの全属性の一覧が表示されます。 **InnerException =>** という見出しが表示されるまで下へスクロールします。  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
  その次の行には、エラーが表示されます。 上の図では、Fabrikam が作成したカスタムの同期規則がエラー元になっています。

エラー自体から十分な情報が得られない場合、データ自体を見る必要があります。 オブジェクト識別子のあるリンクをクリックし、 [システム全体でオブジェクトとそのデータをフォロー](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)できます。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。



<!--HONumber=Jan17_HO2-->



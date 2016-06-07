<properties
	pageTitle="Azure AD Connect 同期: Synchronization Service Manager UI | Microsoft Azure"
	description="Azure AD Connect の Synchronization Service Manager の [操作] タブについて"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="andkjell"/>


# Azure AD Connect 同期: Synchronization Service Manager

[操作](active-directory-aadconnectsync-service-manager-ui-operations.md) | [コネクタ](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [メタバース デザイナー](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [メタバース検索](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

[操作] タブには、最近の操作の結果が表示されます。このタブは問題を理解し、解決するための鍵となります。

## [操作] タブに表示される情報を理解する
上半分にはすべての実行が時系列で表示されます。既定では、操作ログには過去 7 日間の情報が保持されますが、これは[スケジューラ](active-directory-aadconnectsync-feature-scheduler.md)で変更できます。状態が成功を示していない実行を探したい場合があります。見出しをクリックし、並べ替えを変更できます。

**[Status]** (ステータス) 列は最も重要な情報であり、実行関連で最も深刻な問題を示します。最も一般的なステータスを調査の優先度に基づいて簡単にまとめると次のようになります (* はエラー文字列が入ることを意味します)。

状態 | Comment (コメント)
--- | ---
stopped-* | 実行を完了できませんでした。たとえば、リモート システムがダウンし、連絡できない場合などです。
stopped-error-limit | エラーの数が 5,000 を超えています。大量のエラーに起因し、実行が自動的に停止しました。
completed-*-errors | 実行は完了しましたが、エラーが発生し (5,000 件未満)、調査が必要です。
completed-*-warnings | 実行は完了しましたが、一部のデータが予想と異なります。エラーがある場合、通常、これは単なる症状です。エラーを解決するまでは、警告を調査しないでください。
成功 | 問題ありません。

行を選択すると、下の領域が更新され、実行の詳細が表示されます。下の領域の左端には、**Step #** (# は番号) という一覧が表示されることがあります。これは、フォレストに複数のドメインがあり、手順が各ドメインを表す場合にのみ表示されます。ドメイン名は **[Partition]** (パーティション) という見出しにあります。**[Synchronization Statistics]** (同期統計) には、処理された変更の数に関する詳細が表示されます。リンクをクリックすると、変更されたオブジェクトの一覧を取得できます。オブジェクトにエラーがある場合、**[Synchronization Errors]** (同期エラー) の下に表示されます。

## [オペレーション] タブでエラーを解決する
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png) エラーがある場合、エラーのあるオブジェクトとエラー自体がリンクとなり、クリックすると詳細が表示されます。

まず、エラー文字列をクリックしてみます (上の画像の **sync-rule-error-function-triggered**)。最初にオブジェクトの概要が表示されます。実際のエラーを確認するには、**[Stack Trace]** (スタック トレース) ボタンをクリックします。エラーのデバッグ レベル情報が表示されます。

**ヒント:** **[call stack information]** (コール スタック情報) ボックスで右クリックし、**[select all]** (すべて選択) を選択して、**[copy]** (コピー) を選択します。次に、スタックをコピーし、メモ帳など、普段利用しているエディターでエラーを確認できます。

- エラー元が **SyncRulesEngine** の場合、コール スタック情報の最初にオブジェクトの全属性の一覧が表示されます。**InnerException =>** という見出しが表示されるまで下へスクロールします。![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png) 見出しの次の行にエラーが表示されます。上の図では、Fabrikam が作成したカスタムの同期ルールがエラー元になっています。

エラー自体から十分な情報が得られない場合、データ自体を見る必要があります。オブジェクト識別子のあるリンクをクリックし、[システム全体でオブジェクトとそのデータをフォロー](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)できます。

## 次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0525_2016-->
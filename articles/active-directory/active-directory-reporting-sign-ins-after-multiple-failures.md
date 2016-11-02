<properties
    pageTitle="複数のエラー後のサインイン"
    description="サインイン試行に何度も連続して失敗した後に正常にサインインしたユーザーを示すレポート。"
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>


# <a name="sign-ins-after-multiple-failures"></a>複数のエラー後のサインイン
このレポートは、サインイン試行に何度も連続して失敗した後に、正常にサインインしたユーザーを示します。 考えられる原因は次のとおりです。

- ユーザーが自分のパスワードを忘れた</li><li>ユーザーが、正しいパスワードを推測するブルート フォース攻撃の対象になっている

このレポートの結果では、正常なサインインよりも前に行われたサインイン試行の連続失敗回数と、最初に成功したサインインに関連するタイムスタンプを確認できます。

**レポートの設定**: レポートに表示されるまでに行われる必要がある、サインイン試行の連続失敗回数の最小値を構成できます。 この設定を変更する場合、その変更は、既存のレポートに現在表示されている失敗した既存のサインインには適用されないことに注意してください。 変更は、すべての今後のサインインに適用されます。 このレポートの変更は、ライセンスを付与された管理者がのみ行うことができます。


![複数のエラー後のサインイン](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)



<!--HONumber=Oct16_HO2-->



<properties 
	pageTitle="Get Insights: Azure AD のパスワード管理レポート | Microsoft Azure" 
	description="この記事では、レポートを使用して、組織内のパスワード管理操作を把握する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# パスワード管理レポートで運用情報を把握する方法
このセクションでは、Azure Active Directory のパスワード管理レポートを使用して、ユーザーが組織内でどのようにパスワード リセットや変更を使用しているかを表示する方法について説明します。

- [**パスワード管理レポートの概要**](#overview-of-password-management-reports)
- [**パスワード管理レポートの表示方法**](#how-to-view-password-management-reports)
- [**組織内のパスワード リセット登録アクティビティの表示**](#view-password-reset-registration-activity)
- [**組織内のパスワード リセット アクティビティの表示**](#view-password-reset-activity)

## パスワード管理レポートの概要
パスワード リセットをデプロイした後、一般的な手順では、次に組織内でリセットがどのように使用されているかを確認します。たとえば、ユーザーが登録しているパスワード リセット方法、または過去数日間にパスワード リセットが実行された回数を確認できます。次に、現在、[Azure 管理ポータル](https://manage.windowsazure.com)のパスワード管理レポートを使用して確認できる一般的な項目をいくつか示します。

- パスワード リセットを登録した人数
- パスワード リセットを登録したユーザー
- ユーザーが登録しているデータ
- 過去 7 日間で自分のパスワードをリセットしたユーザー数
- パスワードをリセットするためにユーザーまたは管理者が使用する一般的な方法
- パスワード リセットを試みる場合に、ユーザーまたは管理者が直面する一般的な問題
- 自らのパスワードを頻繁にリセットしている管理者
- パスワード リセットに関する不審なアクティビティの有無


## パスワード管理レポートを表示する方法
パスワード管理レポートを参照するには、次の手順に従います。

1.	[Azure 管理ポータル](https://manage.windowsazure.com)で **[Active Directory]** 拡張機能をクリックします。
2.	ポータルに表示される一覧から、ディレクトリを選択します。
3.	**[レポート]** タブをクリックします。
4.	**[動作状況のログ]** セクションの下で、
5.	**[パスワード リセット アクティビティ]** レポートまたは **[パスワード リセット登録アクティビティ]** レポートのいずれかを選択します。

    ![][001]

## パスワード リセット登録アクティビティの表示

パスワード リセット登録アクティビティ レポートでは、組織で実行されたすべてのパスワード リセットの登録が表示されます。パスワード リセットの登録は、パスワード リセット登録ポータル ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) で認証情報を正常に登録したユーザーについて、このレポートに表示されます

- **最大期間**: 1 か月
- **最大行数**: 無制限
- **ダウンロードの可否**: CSV ファイルでダウンロードできます。

    ![][002]

### レポートの列の説明
次の一覧では、レポートの各列について詳細に説明します。

- **ユーザー** – パスワード リセット登録操作を試みたユーザー。
- **ロール** – ディレクトリ内のユーザーの役割。
- **日付と時刻** – 試行の日付と時刻。
- **登録データ** – パスワード リセット登録中にユーザーが提供した認証データ。

### レポートの値の説明
次の表には、各列で使用できるさまざまな値について説明します。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>列</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>使用できる値とその意味</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>登録済みデータ</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>連絡用電子メール</strong> – ユーザーが、連絡用電子メールまたは認証用電子メールを認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>会社電話</strong> – ユーザーが、会社電話を認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>携帯電話</strong> – ユーザーが、携帯電話または認証用の電話を認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>秘密の質問</strong> – ユーザーが、秘密の質問を認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>上の任意の組み合わせ (連絡用電子メール + 携帯電話など)</strong> – 2 ゲート ポリシーを指定した場合に発生し、パスワード リセット要求を認証するためにユーザーがいずれの方法を使用したかを示します。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## パスワード リセット アクティビティの表示

このレポートには、組織で実行されたすべてのパスワード リセット試行が表示されます。

- **最大期間**: 1 か月
- **最大行数**: 無制限
- **ダウンロードの可否**: CSV ファイルでダウンロードできます。

    ![][003]

### レポートの列の説明
次の一覧では、レポートの各列について詳細に説明します。

1. **ユーザー** – パスワード リセット操作を試みたユーザー (ユーザーがパスワードをリセットするときに指定するユーザー ID フィールドに基づきます)。
2. **ロール** – ディレクトリ内のユーザーの役割。
3. **日付と時刻** – 試行の日付と時刻。
4. **使用された方法** – このリセット操作でユーザーが使用した認証方法。
5. **結果** – パスワード リセット操作の最終的な結果。
6. **詳細** – パスワード リセットによって、表示された値についての理由の詳細。予期しないエラーを解決するために実行できる軽減手順も含まれます。

### レポートの値の説明
次の表には、各列で使用できるさまざまな値について説明します。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>列</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>使用できる値とその意味</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用された方法</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>連絡用電子メール</strong> – ユーザーが、連絡用電子メールまたは認証用電子メールを認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>会社電話</strong> – ユーザーが、会社電話を認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>携帯電話</strong> – ユーザーが、携帯電話または認証用の電話を認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>秘密の質問</strong> – ユーザーが、秘密の質問を認証のために使用しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>上の任意の組み合わせ (連絡用電子メール + 携帯電話など)</strong> – 2 ゲート ポリシーを指定した場合に発生し、パスワード リセット要求を認証するためにユーザーがいずれの方法を使用したかを示します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>結果</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>中止</strong> – ユーザーはパスワード リセットを開始しましたが、完了せずに途中で中止しました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>ブロック</strong> - 24 時間以内にパスワード リセット ページまたは単一のパスワード リセット ゲートを多く使用しすぎたため、ユーザーのアカウントで、パスワード リセットの使用が禁止されました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>キャンセル</strong> – ユーザーはパスワード リセットを開始しましたが、途中で [キャンセル] ボタンをクリックして、セッションをキャンセルしました。 <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>管理者への連絡</strong> – 解決できない問題がセッション中に発生したため、ユーザーはパスワード リセット フローを完了せずに、[管理者に連絡] リンクをクリックしました。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>失敗</strong> – おそらくユーザーがこの機能を使用するように構成されていなかったため、パスワードをリセットできませんでした (ライセンスがない、認証情報が不足、パスワードがオンプレミスで管理されているが書き戻しがオフになっている場合など)。 <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>成功</strong> – パスワード リセットは成功しました。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>詳細</p>
              </td>
              <td>
                <p>次の表を参照してください。</p>
              </td>
            </tr>
          </tbody></table>

### [詳細] 列で許可される値
パスワード リセット アクティビティ レポートを使用している場合に、想定される結果の種類の一覧を以下に示します。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>詳細</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>結果の種類</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、電子メールの確認オプションの完了後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、モバイル SMS の確認オプションの完了後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、音声通話の確認オプションの完了後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、会社音声通話の確認オプションの完了後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、秘密の質問オプションの完了後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、ユーザー ID の入力後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、電子メールの確認オプションの開始後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、モバイル SMS の確認オプションの開始後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、音声通話の確認オプションの開始後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、会社音声通話の確認オプションの開始後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、秘密の質問オプションの開始後に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、新しいパスワードを選択する前に中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、新しいパスワードを選択するときに中止しました。</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、非常に多くの無効な電子メール確認コードを入力したため、24 時間ブロックされました。</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、非常に多くの無効な SMS 確認コードを入力したため、24 時間ブロックされました。</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、携帯電話の音声確認を何度も試行したため、24 時間ブロックされました。</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、会社電話の音声確認を何度も試行したため、24 時間ブロックされました。</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、秘密の質問に何度も答えようとしたため、24 時間ブロックされました。</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、電話番号を何度も確認しようとしたため、24 時間ブロックされました。</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、必要な認証方式に合格する前にキャンセルしました。</p>
              </td>
              <td>
                <p>Cancelled</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、新しいパスワードを送信する前にキャンセルしました。</p>
              </td>
              <td>
                <p>Cancelled</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、電子メールの確認オプションを試行した後に、管理者に連絡しました。</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、モバイル SMS の確認オプションを試行した後に、管理者に連絡しました。</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、モバイル音声通話の確認オプションを試行した後に、管理者に連絡しました。</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、会社音声通話の確認オプションを試行した後に、管理者に連絡しました。</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、秘密の質問の確認オプションを試行した後に、管理者に連絡しました。</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>パスワード リセットは、このユーザーに対して有効になっていません。この問題を解決するには、[構成] タブで、パスワード リセットを有効にします。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーには、ライセンスがありません。この問題を解決するには、ユーザーにライセンスを追加します。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーは、Cookie を有効にしていないデバイスからリセットしようとしました。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーのアカウントでは、認証方法が十分に定義されていません。この問題を解決するには、認証情報を追加します。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーのパスワードは、オンプレミスで管理されています。この問題を解決するには、パスワードの書き戻しを有効にします。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>オンプレミス パスワード リセット サービスに接続できませんでした。同期コンピューターのイベント ログを確認してください。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーのオンプレミスのパスワードのリセット中に問題が発生しました。同期コンピューターのイベント ログを確認してください。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>このユーザーは、パスワード リセット ユーザー グループのメンバーではありません。この問題を解決するには、このグループに、ユーザーを追加します。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>パスワード リセットが、このテナントですべて無効になっています。この問題を解決するには、<a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a> を参照してください。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーが、正常にパスワードをリセットしました。</p>
              </td>
              <td>
                <p>Succeeded</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**その他のリソース**


* [パスワード管理とは](active-directory-passwords.md)
* [パスワード管理のしくみ](active-directory-passwords-how-it-works.md)
* [パスワード管理の概要](active-directory-passwords-getting-started.md)
* [パスワード管理のカスタマイズ](active-directory-passwords-customize.md)
* [パスワード管理のベスト プラクティス](active-directory-passwords-best-practices.md)
* [パスワード管理に関する FAQ](active-directory-passwords-faq.md)
* [パスワード管理のトラブルシューティング](active-directory-passwords-troubleshoot.md)
* [詳細情報](active-directory-passwords-learn-more.md)
* [Azure AD のパスワード管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=62-->
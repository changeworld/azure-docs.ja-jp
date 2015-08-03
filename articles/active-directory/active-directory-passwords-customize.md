<properties 
	pageTitle="カスタマイズ: Azure AD Password Management | Microsoft Azure" 
	description="ニーズに合わせて Azure AD で Password Management の外観、動作、および通知をカスタマイズする方法。" 
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

# Password Management を組織のニーズに合わせてカスタマイズする
ユーザーに最良のエクスペリエンスを与えるため、Password Management の使用可能なすべての構成オプションを調べて試してみることをお勧めします。[Azure 管理ポータル](https://manage.windowsazure.com)で、**[Active Directory 拡張機能]** の [構成] タブに移動することで、今すぐ調査を開始できます。このトピックでは、[Azure 管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブから管理者として実行できる以下の Password Management のカスタマイズについて、そのすべてを説明します。

- [**Password Management の外観のカスタマイズ**](#password-managment-look-and-feel)
- [**User Password Management の動作のカスタマイズ**](#password-management-behavior)
- [**Password Management 通知のカスタマイズ**](#password-management-notifications)

## Password Managment の外観
次の表は、各コントロールが、パスワード リセットの登録とリセットを行うユーザーのエクスペリエンスに対して、どのように影響するかを説明しています。これらのオプションは、[Azure 管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブにある **[ディレクトリのプロパティ]** セクションで構成できます。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>ポリシー コントロール</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>説明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>ディレクトリ名</p>
              </td>
              <td>
                <p>パスワード リセット メール通信で、ユーザーまたは管理者に対して表示される組織名を決定します。</p>
              </td>
              <td>
                <p>
                  <strong>”管理者に問い合わせてください” メール:</strong>
                </p>
                <ul>
                  <li class="unordered">
												送信元のフレンドリー名を決定します。例: “Microsoft (<strong>Wingtip Toys の代理として)</strong>”<br><br></li>
                  <li class="unordered">
												メールの件名を決定します。例: "<strong>Wingtip Toys</strong> アカウント メール確認コード"<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット メール:</strong> 
                </p>
                <ul>
                  <li class="unordered">
												送信元のフレンドリー名を決定します。例: “Microsoft (<strong>Wingtip Toys の代理として)</strong>”<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>サインイン ページとアクセス パネル ページの表示</p>
              </td>
              <td>
                <p>パスワード リセット ページにアクセスするユーザーに表示するロゴ (マイクロソフトのロゴまたは独自のカスタム ロゴ) を決定します。この構成項目は、アクセス パネル ページとサインイン ページにブランド化も追加します。</p>
                <p>
                  
                </p>
                <p>テナントのブランド化とカスタマイズ機能については、「<a href="https://technet.microsoft.com/library/dn532270.aspx">サインイン ページとアクセス パネル ページに会社のブランド化を追加</a>」を参照してください。</p>
              </td>
              <td>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												既定のマイクロソフトのロゴの代わりに、カスタム ロゴをパスワード リセット ポータルの上部に表示するかどうかを決定します。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> パスワード リセット ページに直接アクセスした場合、パスワード リセット ポータルの最初のページにカスタム ロゴが表示されないことがあります。ユーザーが自分のユーザー ID を入力して [次へ] をクリックすると、カスタム ロゴが表示されます。パスワード リセット ページに次のような whr パラメーターを渡すことで、ページを読み込むときにカスタム ロゴを表示させることができます: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>”管理者に問い合わせてください” メール:</strong>
                </p>
                <ul>
                  <li class="unordered">
												ユーザーがパスワード リセット UI の [管理者に問い合わせてください] リンクをクリックすることで問い合わせを行うことを選択したときに、管理者に送信されるメールの下部にカスタム ロゴを表示するかどうかを決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット メール:</strong> 
                </p>
                <ul>
                  <li class="unordered">
												ユーザーがパスワードをリセットしたときに、ユーザーに送信されるメールの下部にカスタム ロゴを表示するかどうかを決定します。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Password Management の動作
次の表は、各コントロールが、パスワード リセットの登録とリセットを行うユーザーのエクスペリエンスに対して、どのように影響するかを説明しています。これらのオプションは、[Azure 管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブにある **[ユーザー パスワードのリセット ポリシー]** セクションで構成できます。

> [AZURE.NOTE]これらのポリシー コントロールを表示するには、使用している管理者アカウントに AAD Premium ライセンスが割り当てられている必要があります。<br><br>これらのポリシー コントロールは、パスワードをリセットするエンド ユーザーにのみ適用され、管理者には適用されません。**管理者には、Microsoft が管理者のために指定した連絡用メールと携帯電話の既定のポリシーがあり、これらは変更できません。**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>ポリシー コントロール</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>説明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>パスワードのリセットが有効になっているユーザー</p>
              </td>
              <td>
                <p>このディレクトリのユーザーがパスワードをリセットできるかどうかを決定します。</p>
              </td>
              <td>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] を設定した場合、ユーザーは独自のチャレンジ データを登録できません。<br><br></li>
                  <li class="unordered">
												[はい] を設定した場合、ユーザーは登録ポータル (<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>) に移動して、独自のチャレンジ データを登録できます。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> ユーザーは、パスワード リセットの登録を行う前に、Azure AD Premium または Basic ライセンスが割り当てられている必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] を設定した場合、ユーザーがパスワードをリセットするには管理者に問い合わせる必要があることを示すメッセージが表示されます。<br><br></li>
                  <li class="unordered">
												[はい] を設定した場合、ユーザーは、<a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a> に移動するか、組織 ID サインイン ページの <strong>[アカウントにアクセスできない場合]</strong> リンクをクリックすることで、パスワードを自動的にリセットできます。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> ユーザーは、パスワードをリセットする前に、Azure AD Premium または Basic ライセンスが割り当てられている必要があります。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>パスワード リセットへのアクセスの制限</p>
              </td>
              <td>
                <p>特定のユーザー グループのみがパスワードのリセットを使用できるかどうかを決定します (<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] を設定した場合、ディレクトリのすべてのエンド ユーザーが、<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> でパスワード リセットの登録を実行できます。<br><br></li>
                  <li class="unordered">
												[はい] を設定した場合、<strong>[パスワードをリセットできるグループ]</strong> コントロールに指定されたエンドユーザーだけが <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> でパスワード リセットの登録を実行できます。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] を設定した場合、ディレクトリのすべてのエンド ユーザーがパスワードを変更できます。<br><br></li>
                  <li class="unordered">
												[はい] を設定した場合、<strong>[パスワードをリセットできるグループ]</strong> コントロールに指定されたエンドユーザーだけがパスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>パスワードをリセットできるグループ</p>
              </td>
              <td>
                <p>パスワードをリセットできるエンド ユーザーのグループを決定します </p>
                <p>
                  
                </p>
                <p>(<strong>[パスワード リセットへのアクセスの制限]</strong> が <strong>[はい]</strong> に設定されている場合のみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												グループを指定しないで <strong>[保存]</strong>をクリックすると、<strong>SSPRSecurityGroupUsers</strong> という名前の空のグループが作成されます。<br><br></li>
                  <li class="unordered">
												独自のグループを指定する場合は、独自の表示名を指定できます。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												<strong>[パスワード リセットへのアクセスの制限]</strong> が <strong>[はい]</strong> に設定されている場合、このグループのエンド ユーザーだけがパスワード リセットの登録を実行できます。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												<strong>[パスワード リセットへのアクセスの制限]</strong> が <strong>[はい]</strong> に設定されている場合、このグループのエンド ユーザーだけがパスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーが使用できる認証方法</p>
              </td>
              <td>
                <p>ユーザーがパスワードをリセットするために使用できるチャレンジを決定します </p>
                <p>
                  
                </p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												少なくとも 1 つのオプションを選択する必要があります。<br><br></li>
                  <li class="unordered">
												ユーザーがパスワードを柔軟にリセットできるように、少なくとも 2 つのオプションを有効にすることを強くお勧めします。<br><br></li>
                  <li class="unordered">
												セキュリティの質問を使用する場合、セキュリティの質問は電話またはメール ベースのパスワードのリセット方法よりも安全性が低いため、別の認証方法と併せて使用することを強くお勧めします。<br><br></li>
                </ul>
                <p>
                  <strong>使用されるディレクトリ フィールド</strong>
                </p>
                <ul>
                  <li class="unordered">
												[会社電話] は、ディレクトリ内のユーザー オブジェクトの <strong>[会社電話]</strong> 属性に対応します。<br><br></li>
                  <li class="unordered">
												[携帯電話] は、ディレクトリ内のユーザー オブジェクトの <strong>[認証用モバイル]</strong> 属性 (パブリックに表示されません)、または<strong>[携帯電話]</strong> 属性 (パブリックに表示されます) のいずれかに対応します。<strong>[認証用電話]</strong> のデータが先にチェックされ、それが存在しない場合は <strong>[携帯電話]</strong> 属性がチェックされます。<br><br></li>
                  <li class="unordered">
												[連絡用メール アドレス] は、ディレクトリ内のユーザー オブジェクトの <strong>[認証用電子メール]</strong> 属性 (パブリックに表示されません) または <strong>[連絡用電子メール]</strong> のいずれかに対応します。<strong>[認証用電子メール]</strong> のデータが先にチェックされ、それが存在しない場合は <strong>[連絡用電子メール]</strong> 属性がチェックされます。<br><br></li>
                  <li class="unordered">
												セキュリティの質問は、ディレクトリ内のユーザー オブジェクトに非公開かつ安全に保存され、登録時にユーザーだけが回答できます。安全保護のため、現時点では、管理者がこれらの回答を編集または表示する方法はありません。<br><br></li>
                  <li class="unordered">
                    <strong>注: </strong>既定では、クラウド属性の [会社電話] と [携帯電話] だけが、オンプレミス ディレクトリからクラウド ディレクトリに同期されます。クラウドに同期されるオンプレミス属性の詳細については、「<a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Azure Active Directory に同期される属性</a>」を参照してください 。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												ユーザーが登録を行うときに表示される認証メソッドに影響します。特定の認証方法を有効にしない場合、ユーザーはその認証メソッドでは自己登録できなくなります。<br><br></li>
                  <li class="unordered">
                    <strong>注: </strong>ユーザーは、現時点では、会社の電話番号を登録することはできません。その認証方法は管理者が定義する必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												特定の確認手順でユーザーがチャレンジとして使用できる認証方法を決定します。たとえば、Azure Active Directory で、ユーザーの <strong>[会社電話]</strong> フィールドと <strong>[認証用電話]</strong> フィールドの両方にデータがある場合は、これらの認証方法のいずれかを使用してパスワードを回復できます。<br><br></li>
                  <li class="unordered">
                    <strong>注: </strong>管理者が有効にした認証メソッドにデータがある場合にのみ、ユーザーはパスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>必要な認証方法の数</p>
              </td>
              <td>
                <p>ユーザーがパスワードをリセットするために通過する必要がある認証方法の最小数を決定します </p>
                <p>
                  
                </p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												必要な認証方法を 1 つまたは 2 つ設定できます。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												登録を完了する前に、ユーザーが登録する必要がある認証方法の最小数を決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												ユーザーがパスワードをリセットする前に通過する必要がある確認手順の数に影響します。確認手順は、ユーザーが認証情報の 1 つを使用してアカウントを確認するように定義されます (会社電話番号への電話または連絡用電子メールへのメールなど)。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> ユーザーのアカウントに、設定されたポリシーに従ってパスワードを問題なくリセットするために必要な認証情報が定義されていない場合は、管理者にパスワードのリセットを依頼することを指示するエラー ページが表示されます。 <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>登録するために必要な質問の数</p>
              </td>
              <td>
                <p>[セキュリティの質問] オプションを登録するときに、ユーザーが回答する必要がある質問の最小数を決定します </p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												登録するために必要な 3 ～ 5 個の質問を設定できます。<br><br></li>
                  <li class="unordered">
												登録するために必要な質問の数は、リセットするために必要な質問の数以上にする必要があります。<br><br></li>
                  <li class="unordered">
												ユーザーが柔軟にパスワードをリセットできるように、登録するために必要な質問の数は、リセットするために必要な質問の数よりも大きい数を設定することをお勧めします。これは、ユーザーに回答を求める質問を、ユーザーが登録したすべての質問の中からランダムに選択できるため、より安全な構成でもあります。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												ユーザーがパスワードをリセットするための登録が完了したとみなされる前に、ユーザーが回答する必要がある質問の最小数を決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>リセットするために必要な質問の数 </p>
              </td>
              <td>
                <p>ユーザーがパスワードをリセットするときに回答する必要がある質問の最小数を決定します </p>
                <p>
                  
                </p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												リセットするために必要な 3 ～ 5 個の質問を設定できます。<br><br></li>
                  <li class="unordered">
												リセットするために必要な質問の数は、登録するために必要な質問の数未満にする必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												ユーザーがパスワードをリセットする前に回答する必要がある質問の最小数を決定します。<br><br></li>
                  <li class="unordered">
												パスワードのリセット時に、この数の質問が、ユーザーが登録したすべての質問の一覧からランダムに選択されます。たとえば、ユーザーが 5 つの質問を登録している場合、ユーザーがパスワードをリセットするときに、これら 5 つの質問の中から、3 つの質問がランダムに選択されます。ユーザーは、パスワードをリセットする前に、すべての質問に正しく答える必要があります。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>セキュリティの質問</p>
              </td>
              <td>
                <p>パスワード リセットの登録時とパスワードのリセット時に、ユーザーが選択できるセキュリティの質問を定義します </p>
                <p>
                  
                </p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												最大 20 個の質問を定義できます。<br><br></li>
                  <li class="unordered">
												質問の最大文字数は 200 文字です。<br><br></li>
                  <li class="unordered">
												回答の最小文字数は 3 文字です。<br><br></li>
                  <li class="unordered">
												回答の最大文字数は 40 文字です。<br><br></li>
                  <li class="unordered">
												ユーザーは、同じ質問に 2 度回答することはできません。<br><br></li>
                  <li class="unordered">
												ユーザーは、2 つの異なる質問に同じ回答をすることはできません。<br><br></li>
                  <li class="unordered">
												Unicode 文字を含む任意の文字セットを使用して、質問と回答を定義できます。<br><br></li>
                  <li class="unordered">
												定義する質問の数は、登録するために必要な質問の数以上にする必要があります。<br><br></li>
                  <li class="unordered">
												ロケールごとに異なる質問を定義することはまだサポートされていませんが、将来実現される予定です。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												パスワード リセットの登録時にユーザーが回答できる質問を決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												パスワードをリセットするためにユーザーが使用できる質問を決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーが初めてアクセス パネルにサインインするときに登録を要求しますか?</p>
                <p>
                  
                </p>
              </td>
              <td>
                <p>ユーザーが次回アクセス パネルにサインインしたときに、パスワードをリセットするための連絡先情報の登録をユーザーに対して要求するかどうかを決定します </p>
                <p>
                  
                </p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												この機能を無効にした場合は、ユーザーを手動で<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> に送って、連絡先データを登録することもできます。 <br><br></li>
                  <li class="unordered">
												ユーザーは、アクセス パネルの [プロファイル] タブにある <strong>[パスワード リセットの登録]</strong> リンクをクリックすることで登録ポータルにアクセスすることもできます。<br><br></li>
                  <li class="unordered">
												この方法による登録は、[キャンセル] ボタンをクリックするかウィンドウを閉じることでキャンセルできますが、ユーザーが登録していない場合は、サインインするたびに登録を求められます。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												この設定は、登録ポータルの動作には影響しません。ユーザーがアクセス パネルにサインインしたときに登録ポータルが表示されるかどうかを決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>ユーザーによる連絡先データの確認が必要になるまでの日数</p>
              </td>
              <td>
                <p><strong>[ユーザーに登録を要求しますか?]</strong> がオンになっている場合、この設定は、ユーザーがデータを再確認するまでの経過時間を決定します。</p>
                <p>
                  
                </p>
                <p><strong>[ユーザーが初めてアクセス パネルにサインインするときに登録を要求しますか?]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												0 ～ 730 日の値を指定できます。0 日は、ユーザーが連絡先データの再確認を求められることはないことを意味します。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												この設定は、登録ポータルの動作には影響しません。ユーザーの連絡先情報を再確認する必要があるときに登録ポータルが表示されるかどうかを決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>[管理者に問い合わせてください] リンクをカスタマイズしますか?</p>
              </td>
              <td>
                <p>エラーが発生したとき、またはユーザーの操作が完了しないときに、パスワード リセット ポータルの左側に表示される [管理者に問い合わせてください] リンクが、カスタム URL またはメール アドレスをポイントするかどうかを制御します。</p>
                <p>
                  
                </p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												この設定を有効にした場合は、この設定のすぐ下にある <strong>[カスタム電子メール アドレスまたは URL]</strong> フィールドに入力して、カスタム URL またはメール アドレスを選択する必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] に設定した場合、ユーザーが強調表示されているリンクをクリックすると、すべてのテナント管理者のプライマリ メール アドレスに、ユーザーのパスワードをリセットすることを要求するメールが送信されます。このメールは、以下のロジックに従って送信されます。<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
														パスワード管理者がいる場合は、すべてのパスワード管理者 (最大 100 人の受信者) にメールを送信します。<br><br></li>
                      <li class="unordered">
														パスワード管理者がいない場合は、すべてのユーザー管理者 (最大 100 人の受信者) にメールを送信します。<br><br></li>
                      <li class="unordered">
														ユーザー管理者がいない場合は、すべてのグローバル管理者 (最大 100 人の受信者) にメールを送信します。<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
												[はい] を設定した場合、この設定は、強調表示されたリンクの動作をカスタマイズして、ユーザーがパスワードのリセットに関するヘルプを得るために移動できるカスタム URL またはメール アドレスを指定します。<br><br></li>
                  <li class="unordered">
												URL を指定した場合は新しいタブで開きます。<br><br></li>
                  <li class="unordered">
												メール アドレスを指定した場合は、そのメール アドレスへの mailto リンクが作成されます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>カスタム電子メール アドレスまたは URL</p>
              </td>
              <td>
                <p><strong>[管理者に問い合わせてください]</strong> リンクがポイントするメール アドレスまたは URL を制御します </p>
                <p>
                  
                </p>
                <p>( <strong>[管理者に問い合わせてください] リンクをカスタマイズしますか?]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												有効なイントラネットまたはエクストラネットの URL またはメール アドレスにする必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												<strong>[管理者に問い合わせてください]</strong> リンクがポイントする場所を変更します。<br><br></li>
                  <li class="unordered">
												メール アドレスを指定した場合、リンクはそのメール アドレスへの “mailto” リンクになります。<br><br></li>
                  <li class="unordered">
												URL を指定した場合、リンクは、その URL を新しいタブで開く標準的な href になります。 <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>オンプレミス ディレクトリへのパスワードの書き戻し</p>
              </td>
              <td>
                <p>このディレクトリで Password Writeback が有効になっているかどうかを制御します。書き戻しがオンの場合は、オンプレミスの書き戻しサービスの状態を示します。</p>
                <p>
                  
                </p>
                <p>これは、機能停止が発生したためサービスを一時的に無効にする場合に便利です。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
												このコントロールは、Azure AD Connect の最新バージョンをダウンロードして Password Writeback をインストールし、<strong>[オプション機能]</strong> 選択画面で <strong>[Password Writeback]</strong> オプションを有効にした場合にのみ表示されます。<br><br></li>
                  <li class="unordered">
												Password Writeback を有効にしたときに、サービスに構成の問題があると感じる場合は、このタブにアクセスして、<strong>[パスワードの書き戻しサービスの状態]</strong> ラベルを見ることで、問題があるかどうかを確認できます。<br><br></li>
                  <li class="unordered">
												表示できる状態は、次のとおりです。<br><br><ul><li class="unordered"><strong>構成済み</strong> – すべてが期待どおりに動作しています<br><br></li><li class="unordered"><strong>未構成</strong> – 書き戻しがインストールされている必要がありますが、そのサービスにアクセスできません。443 への発信接続がブロックされていないことを確認し、問題が解決しない場合はサービスを再インストールします。<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												書き戻しがデプロイされ、構成されているときに、このスイッチを <strong>[いいえ]</strong> に設定すると、欠き戻しは無効になり、フェデレーション ユーザーとパスワード ハッシュ同期ユーザーは、パスワード リセットの登録を行うことはできません。<br><br></li>
                  <li class="unordered">
												スイッチが <strong>[はい]</strong> に設定されている場合、書き込みは有効になり、フェデレート ユーザーとパスワード ハッシュ同期ユーザーは、パスワードをリセットできます。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												書き戻しがデプロイされ、構成されているときに、このスイッチを <strong>[いいえ]</strong> に設定すると、書き戻しは無効になり、フェデレート ユーザーとパスワード ハッシュ同期ユーザーは、パスワードをリセットできません。<br><br></li>
                  <li class="unordered">
												スイッチが <strong>[はい]</strong> に設定されている場合、書き込みは有効になり、フェデレート ユーザーとパスワード ハッシュ同期ユーザーは、パスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Password Management 通知のカスタマイズ
次の表は、各コントロールが、パスワード リセット通知を受信するユーザーと管理者のエクスペリエンスに対して、どのように影響するかを説明しています。これらのオプションは、[Azure 管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブにある **[通知]** セクションで構成できます。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>ポリシー コントロール</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>説明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>管理者が自分のパスワードをリセットしたときに、他の管理者に通知します。</p>
              </td>
              <td>
                <p>別の種類の管理者がパスワードをリセットしたときに、すべてのグローバル管理者にプライマリ メール アドレスを使用してメールで通知するかどうかを決定します。</p>
              </td>
              <td>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] を設定した場合、通知は送信されません。<br><br></li>
                  <li class="unordered">
												[はい] を設定した場合は、1 人の管理者が自分のパスワードをリセットしたときに、本人以外のすべてのグローバル管理者に通知されます。<br><br></li>
                  <li class="unordered">
												この通知は、組織内の本人以外のすべてのグローバル管理者のプライマリ電子メール アドレスにメールで送信されます。<br><br></li>
                </ul>
                <p>
                  <strong>例:</strong>
                </p>
                <ul>
                  <li class="unordered">
												このオプションが有効になっている場合、管理者 A が自分のパスワードをリセットしたときに、B、C、および D という 3 人の管理者がいると、管理者 B、C、および D は、管理者 A がパスワードをリセットしたことを示す電子メールを受信します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>パスワードがリセットされたときにユーザーおよび管理者に通知する</p>
              </td>
              <td>
                <p>パスワードをリセットしたエンド ユーザーまたは管理者が、パスワードがリセットされたことを知らせるメールを受信するかどうかを決定します。</p>
              </td>
              <td>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
												[いいえ] を設定した場合、通知は送信されません。<br><br></li>
                  <li class="unordered">
												[はい] を設定した場合、ユーザーまたは管理者は、自分のパスワードをリセットするたびに、パスワードがリセットされたことを示す通知を受信します。<br><br></li>
                  <li class="unordered">
												この通知は、パスワードをリセットしたユーザーのプライマリと連絡用 (または認証用) のメール アドレスにメールで送信されます。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/> <br/> <br/>

**その他のリソース**


* [パスワード管理とは](active-directory-passwords.md)
* [パスワード管理のしくみ](active-directory-passwords-how-it-works.md)
* [パスワード管理の概要](active-directory-passwords-getting-started.md)
* [パスワード管理のベスト プラクティス](active-directory-passwords-best-practices.md)
* [パスワード管理レポートで運用情報を把握する方法](active-directory-passwords-get-insights.md)
* [パスワード管理に関する FAQ](active-directory-passwords-faq.md)
* [パスワード管理のトラブルシューティング](active-directory-passwords-troubleshoot.md)
* [詳細情報](active-directory-passwords-learn-more.md)
* [MSDN のパスワード管理](https://msdn.microsoft.com/library/azure/dn510386.aspx) 

<!---HONumber=July15_HO4-->
<properties 
	pageTitle="Azure AD Connect の管理" 
	description="Azure AD Connect の既定の構成と運用タスクを拡張する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect の管理 

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="概要">概要</a> <a href="../active-directory-aadconnect-how-it-works/" title="動作">動作</a> <a href="../active-directory-aadconnect-get-started/" title="使用">使用</a> <a href="../active-directory-aadconnect-whats-next/" title="次の手順" class="current">次の手順</a> <a href="../active-directory-aadconnect-learn-more/" title="詳細情報">詳細情報</a>
</div>

組織のニーズや要件を満たすように Azure Active Directory Connect をカスタマイズするための高度な運用に関するトピックを次に示します。

## Azure AD Premium および Enterprise Mobility のユーザーにライセンスを割り当てる

ユーザーがクラウドに同期されたため、Office 365 などのクラウド アプリを利用できるように、そのユーザーにライセンスを割り当てる必要があります。

### Azure AD Premium または Enterprise Mobility Suite のライセンスを割り当てるには
--------------------------------------------------------------------------------
<ol>
<li>Azure ポータルに管理者としてサインインします。</li>
<li>左側で、**[Active Directory]** を選択します。</li>
<li>[Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。</li>
<li>[ディレクトリ] ページの上部にある **[ライセンス]** を選択します。</li>
<li>[ライセンス] ページで、[Active Directory Premium] または [Enterprise Mobility Suite] を選択し、**[割り当て]** をクリックします。</li>
<li>ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。</li>


## スケジュールされた同期タスクを確認する
同期の状態を確認する場合は、Azure ポータルで確認します。

### スケジュールされた同期タスクを確認するには
--------------------------------------------------------------------------------

1. Azure ポータルに管理者としてサインインします。
2. 左側で、**[Active Directory]** を選択します。
3. [Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある **[ディレクトリ統合]** を選択します。
5. [ローカル Active Directory との統合] で、最後の同期時刻を確認します。

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## スケジュールされた同期タスクの開始
同期タスクを実行する必要がある場合は、Azure AD Connect ウィザードを再度実行して行うことができます。Azure AD 資格情報を入力する必要があります。ウィザードで、**[同期オプションのカスタマイズ]** タスクを選択し、[次へ] をクリックします。最後に、**[初期構成が完了したら、同期処理を開始してください。]** チェック ボックスがオンになっていることを確認してください。

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




## Azure AD Connect で利用可能なその他のタスク
Azure AD Connect の初回インストール後は、Azure AD Connect のスタート ページまたはデスクトップのショートカットから、いつでもウィザードを再び起動できます。再度ウィザードを実行すると、追加のタスクの形式で新しいオプションがいくつか表示されます。

次の表に、これらのタスクの概要と各タスクの簡単な説明を示します。

<center>![Join Rule](./media/active-directory-aadconnect-whats-next/addtasks.png) </center>

その他のタスク | 説明 
------------- | ------------- |
選択したシナリオを表示する |現在の Azure AD Connect のソリューションを表示できます。これには、全般設定、同期されたディレクトリ、同期設定などが含まれます。
同期オプションをカスタマイズする | 構成への Active Directory フォレストの追加、ユーザー、グループ、デバイス、パスワード ライトバックなどの同期オプションの有効化など、現在の構成を変更できます。
ステージング モードを有効にする | これを使用すると、後で同期される情報をステージングできますが、Azure AD または Active Directory には何もエクスポートされません。事前に同期をプレビューすることができます。


 






## その他のドキュメント
Azure AD Connect の操作に関するその他のドキュメントについては、次を参照してください。

- [Azure AD Connect の既定の構成の変更](active-directory-aadconnect-whats-next-change-default-config.md)
- [Azure AD Connect 同期規則エディターの使用](active-directory-aadconnect-whats-next-synch-rules-editor.md)
- [宣言型のプロビジョニングの使用](active-directory-aadconnect-whats-next-declarative-prov.md)

また、Azure AD Sync 用に作成されたドキュメントの一部は、まだ Azure AD Connect と関連があり、Azure AD Connect に当てはまります。Azure.com にこのドキュメントを提供するためにあらゆる努力を重ねていますが、このドキュメントの一部は、現在も MSDN の範囲のライブラリに掲載されています。その他のドキュメントについては、「[MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)」と「[MSDN の Azure AD Sync](https://msdn.microsoft.com/library/azure/dn790204.aspx)」を参照してください。


 

<!---HONumber=July15_HO4-->
<properties
   pageTitle="Azure AD Connect: 旧バージョンからアップグレードする | Microsoft Azure"
   description="インプレース アップグレードやスウィング移行など、Azure Active Directory Connect を最新リリースにアップグレードするさまざまな方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする
このトピックでは、Azure AD Connect のインストールを最新リリースにアップグレードするさまざまな方法について説明します。Azure AD Connect を常に最新リリースにしておくことをお勧めします。

DirSync からアップグレードする場合は、代わりに「[Azure AD Connect: Windows Azure Active Directory 同期 (DirSync) のアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md)」を参照してください。

Azure AD Connect のアップグレードには複数の方法があります。

メソッド | Description
--- | ---
[自動アップグレード](active-directory-aadconnect-feature-automatic-upgrade.md) | 高速インストールのユーザーの場合、これは最も簡単な方法です。
[インプレース アップグレード](#in-place-upgrade) | サーバーが 1 台だけの場合は、同じサーバーでインストールをインプレース アップグレードします。
[スウィング移行](#swing-migration) | 2 台のサーバーを用意し、一方に新しいリリースを準備して、アクティブなサーバーを変更します。

必要なアクセス許可については、[アップグレードに必要なアクセス許可](active-directory-aadconnect-accounts-permissions.md#upgrade)を参照してください。

## インプレース アップグレード
インプレース アップグレードは、Azure AD Sync または Azure AD Connect からの移動に使用できます。DirSync または FIM + Azure AD コネクタのソリューションには使用できません。

この方法は、サーバーが 1 台でオブジェクトが約 100,000 未満の場合にお勧めします。標準の同期規則に対する変更があった場合は、アップグレード後にフル インポートと完全同期が実行されます。これにより、新しい構成がシステムのすべての既存のオブジェクトに適用されることが保証されます。同期エンジンのスコープ内のオブジェクトの数によっては、数時間かかることがあります。通常の定期差分同期 (既定では 30 分ごと) は中断されますが、パスワード同期は続行されます。インプレース アップグレードは週末に実行するよう検討してください。新しい Azure AD Connect リリースで標準構成に変更がなかった場合は、フル インポートまたは完全同期ではなく通常の差分インポートまたは差分同期が開始されます。

![インプレース アップグレード](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

標準の同期規則を変更した場合は、アップグレードの間に既定の構成に戻ります。アップグレードの間に構成が維持されるようにするには、「[Azure AD Connect Sync: 既定の構成を変更するためのベスト プラクティス](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)」の説明に従って変更を行ってください。

## スウィング移行
複雑なデプロイまたは非常に多くのオブジェクトがある場合、ライブ システムでのインプレース アップグレードは困難である場合があります。ユーザーによっては数日かかることがあり、この間差分変更は処理されません。

代わりに、スウィング移行を使用することをお勧めします。この方法では、アクティブ サーバーが 1 台とステージング サーバーが 1 台、(少なくとも) 2 台のサーバーが必要です。アクティブ サーバー (次の図の青い実線) は、アクティブな負荷を処理します。ステージング サーバー (次の図の紫の破線) は新しいリリースの準備用であり、このサーバーの準備が完全にできたらアクティブにします。古いバージョンがインストールされている前のアクティブ サーバーは、ステージング サーバーにしてアップグレードします。

2 つのサーバーには、それぞれ異なるバージョンを使用できます。たとえば、使用を停止する予定のアクティブ サーバーでは Azure AD Sync を使用し、新しいステージング サーバーでは Azure AD Connect を使用することができます。

![ステージング サーバー](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

注: 3 台または 4 台のサーバーでこの方法を行うのが好まれる場合があります。ステージング サーバーでアップグレードが行われるため、その間は[障害復旧](active-directory-aadconnectsync-operations.md#disaster-recovery)用のバックアップ サーバーがありません。最大 4 台のサーバーを使用すると、新しいバージョンにアップグレードされた新しいプライマリ/スタンバイ サーバーのセットを準備でき、引き継ぎ用のステージング サーバーを常に確保できます。

以下の手順は、Azure AD Sync または FIM + Azure AD コネクタのソリューションからの移行にも使用できます。この手順は DirSync には使用できませんが、DirSync 用の手順が組み込まれた同じスウィング移行 (並列デプロイとも呼ばれます) 方法が、[Azure Active Directory 同期 (DirSync) のアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md)に関するページで説明されています。

### スウィング移行の手順

1. 両方のサーバーで Azure AD Connect を使用する場合は、アップグレードを開始する前に、アクティブ サーバーとステージング サーバーがどちらも同じバージョンを使用していることを確認します。同じバージョンを使用することで、後で違いを比較しやすくなります。Azure AD Sync からアップグレードする場合、2 つのサーバーのバージョンは異なるものになります。
2. カスタム構成を行っていて、ステージング サーバーにはそれが含まれない場合は、後の「[カスタム構成をアクティブ サーバーからステージング サーバーに移動する](#move-custom-configuration-from-active-to-staging-server)」の手順に従ってください。
3. Azure AD Connect の以前のリリースからアップグレードする場合は、ステージング サーバーを最新のバージョンにアップグレードします。Azure AD Sync から移行する場合は、ステージング サーバーに Azure AD Connect をインストールします。
4. 同期エンジンがステージング サーバーで完全なインポートと完全な同期を実行するまで待ちます。
5. 「[サーバーの構成の確認](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)」の「**確認**」の手順を使用して、新しい構成で予期しない変更が発生しなかったことを確認します。予期しないことがあった場合は、データに問題がなくなるまで、修正、インポートと同期の実行、確認を繰り返します。これらの手順については、リンクされているトピックを参照してください。
6. ステージング サーバーをアクティブ サーバーに切り替えます。これは、「[サーバーの構成の確認](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)」の最後の手順である「**アクティブなサーバーの切り替え**」にあたります。
7. Azure AD Connect をアップグレードする場合は、ステージング モードになっているサーバーを最新リリースにアップグレードします。前と同じ手順に従って、データと構成をアップグレードします。Azure AD Sync から移行している場合は、ここで、以前のサーバーの電源を切って、使用を停止できます。

### カスタム構成をアクティブ サーバーからステージング サーバーに移動する
アクティブ サーバーの構成を変更してある場合、ステージング サーバーにも同じ変更を適用する必要があります。

作成したカスタム同期規則は、PowerShell で移動できます。その他の変更は、移行はできないため、両方のシステムで同じように適用する必要があります。

両方のサーバーで同じように構成されていることを確認する必要がある項目は次のとおりです。

- 同じフォレストへの接続。
- ドメインと OU のすべてのフィルター処理。
- 同じオプション機能 (パスワード同期やパスワード ライトバックなど)。

**同期規則の移動**カスタム同期規則を移動するには次のようにします。

1. アクティブ サーバーで**同期規則エディター**を開きます。
2. カスタム規則を選択します。**[エクスポート]** をクリックします。メモ帳ウィンドウが表示されます。一時ファイルを拡張子 PS1 で保存します。これにより、PowerShell スクリプトになります。ps1 ファイルをステージング サーバーにコピーします。 ![同期規則のエクスポート](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. コネクタの GUID は、ステージング サーバーでは異なります。GUID を取得するには、**同期規則エディター**を開始し、同じ接続先システムを表す既定の規則のいずれかを選択して、**[エクスポート]** をクリックします。PS1 ファイルの GUID を、ステージング サーバーから取得した GUID に置き換えます。
4. PowerShell プロンプトで、PS1 ファイルを実行します。これにより、ステージング サーバーにカスタム同期規則が作成されます。
5. 複数のカスタム規則がある場合は、すべてのカスタム規則について繰り返します。

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0928_2016-->
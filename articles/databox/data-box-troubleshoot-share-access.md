---
title: Azure Data Box へのデータのコピー時の共有接続エラーのトラブルシューティング | Microsoft Docs
description: Azure Data Box へのデータのコピー時に SMB 共有接続を妨げているネットワークの問題を特定する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/23/2021
ms.author: alkohli
ms.openlocfilehash: afc76602b610488fd2ce4cf7f17e547821fc406c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868632"
---
# <a name="troubleshoot-share-connection-failure-during-data-copy-to-azure-data-box"></a>Azure Data Box へのデータのコピー時の共有接続エラーのトラブルシューティング

この記事では、ネットワークの問題が発生して、Azure Data Box デバイス上の SMB 共有に接続できない場合の対応について説明します。

デバイス上の共有に接続できない最も一般的な理由は次のとおりです。

- [ドメインの問題](#check-for-a-domain-issue)
- [アカウントが共有からロックアウトされている](#account-locked-out-of-share)
- [グループ ポリシーによって接続が妨げられている](#check-for-a-blocking-group-policy)
- [アクセス許可の問題](#check-for-permissions-issues)

## <a name="check-for-a-domain-issue"></a>ドメインの問題を確認する

ドメインの問題が共有への接続を妨げているかどうかを確認するには、次のようにします。

- デバイスに接続することを試み、次のいずれかの形式を使用してユーザー名を入力します。

    - `<device IP address>\<user name>`
    - `\<user name>`

デバイスに接続できる場合は、ドメインの問題によって共有への接続が妨げられていません。

## <a name="account-locked-out-of-share"></a>アカウントが共有からロックアウトされている

正しくないパスワードで共有に接続しようとして 5 回失敗すると、共有がロックされて 15 分間接続できません。
 
失敗した接続の試行には、バックグラウンド プロセス (再試行など) が含まれていることがあり、これらは認識されていない場合があります。

> [!NOTE]
> Data Box バージョン 4.0 以前の古いデバイスがある場合は、ログイン試行が 3 回失敗した後にアカウントが 30 分間ロックされます。

**エラーの説明**: 共有へのアクセス方法に応じて、次のいずれかのエラーが表示されます。

- SMB 経由でホスト コンピューターから接続しようとしている場合は、"参照されているアカウントは現在ロックアウトされており、ログオンできない可能性があります" というエラーが表示されます。

  次の例は、このような接続試行の出力を示しています。

  ```
  C:\Users\Databoxuser>net use \\10.100.100.10\mydbresources_BlockBlob /u:10.100.100.10\mydbresources
  Enter the password for '10.100.100.10\mydbresources' to connect to '10.100.100.10':
  System error 1909 has occurred.
  
  The referenced account is currently locked out and may not be logged on to.
  ```

- データ コピー サービスを使用している場合は、デバイスのローカル Web UI に次の通知が表示されます。

  ![Data Box のローカル Web UI の [通知] ペインのスクリーンショット。 ロックされた共有アカウントの通知が強調表示されています。](media/data-box-troubleshoot-share-access/share-lock-01.png)


**推奨される解決方法**: 共有アカウントのロックアウト後に SMB 共有に接続するには、次の手順を実行します。

1. 共有の SMB 資格情報を確認します。 デバイスのローカル Web UI で、 **[接続とコピー]** に移動して、共有に **[SMB]** を選択します。 次のダイアログ ボックスが表示されます。

    ![Data Box の SMB 共有の [共有へのアクセスとデータのコピー] 画面のスクリーンショット。 アカウント、ユーザー名、パスワードのコピー アイコンが強調表示されています。](media/data-box-troubleshoot-share-access/get-share-credentials-01.png)

1. ロックアウト期間 (15 分または 30 分) が終了すると、ロックが解除されます。 共有に接続できるようになりました。

   - SMB 経由でホスト コンピューターから共有に接続するには、次のコマンドを実行します。 手順については、「[Azure Data Box に SMB 経由でデータをコピーする](data-box-deploy-copy-data.md#connect-to-data-box)」を参照してください。
  
     `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

   - データ コピー サービスを使用して共有に接続するには、次に示されているような、ユーザー アカウントのロックが解除されたことを示す通知を確認します。 **[データのコピー]** ペインで、[Data Box にデータをコピーする](data-box-deploy-copy-data-via-copy-service.md#copy-data-to-data-box)ことができるようになりました。

     ![Data Box のローカル Web UI の [データのコピー] ペインのスクリーンショット。共有ユーザー アカウントのロックが解除されたことの通知と [データのコピー] オプションが強調表示されています。](media/data-box-troubleshoot-share-access/share-lock-02.png)


## <a name="check-for-a-blocking-group-policy"></a>ブロックしているグループ ポリシーを確認する

クライアント/ホスト コンピューター上のグループ ポリシーによって共有への接続が妨げられているかどうかを確認します。 可能な場合は、クライアント/ホスト コンピューターを、グループ ポリシー オブジェクト (GPO) が適用されていない組織単位 (OU) に移動します。

Data Box の共有へのアクセスを妨げているグループ ポリシーがないことを確認するには、次のようにします。

* クライアント/ホスト コンピューターが Active Directory の独自の OU にあることを確認します。

* クライアント/ホスト コンピューターに GPO が適用されていないことを確認します。 クライアント/ホスト コンピューター (子ノード) が親から GPO を自動的に継承しないように、継承をブロックすることができます。 詳細については、「[継承をブロックする](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11))」を参照してください。

## <a name="check-for-permissions-issues"></a>アクセス許可の問題を確認する

ドメインに問題がなく、グループ ポリシーによって共有へのアクセスがブロックされていない場合は、監査ログとセキュリティ イベント ログを確認して、デバイスのアクセス許可の問題を確認します。

### <a name="review-security-event-logs"></a>セキュリティ イベント ログを確認する

認証の失敗を示すエラーについては、デバイスの Windows セキュリティ イベント ログを確認します。

`etw` フォルダー内の `Smbserver.Security` イベント ログを確認するか、イベント ビューアーでセキュリティ エラーを表示することができます。

イベント ビューアーで Windows セキュリティ イベント ログを確認するには、次の手順を実行します。

1. Windows イベント ビューアーを開くには、 **[スタート]** 画面で「**イベント ビューアー**」と入力して、Enter キーを押します。

1. イベント ビューアーのナビゲーション ペインで、 **[Windows ログ]** を展開し、 **[セキュリティ]** フォルダーを選択します。

    ![[セキュリティ] のイベントが表示されている Windows イベント ビューアーのスクリーンショット。 Windows フォルダーと [セキュリティ] サブフォルダーが強調表示されています。](media/data-box-troubleshoot-share-access/event-viewer-01.png)

3. 次のいずれかのエラーを探します。

    エラー 1:

    ```xml
    SMB Session Authentication Failure
    Client Name: \\<ClientIP>
    Client Address: <ClientIP:Port>
    User Name:
    Session ID: 0x100000000021
    Status: The attempted logon is invalid. This is either due to a bad username or authentication information. (0xC000006D)
    SPN: session setup failed before the SPN could be queried
    SPN Validation Policy: SPN optional / no validation
    ```
      
    エラー 2:
    ```xml
     LmCompatibilityLevel value is different from the default.
    Configured LM Compatibility Level: 5
    Default LM Compatibility Level: 3   
    ```

    どちらのエラーも、デバイスで LAN Manager 認証レベルを変更する必要があることを示しています。
 
### <a name="change-lan-manager-authentication-level"></a>LAN Manager 認証レベルを変更する
 
デバイスの LAN Manager 認証レベルを変更するには、[ローカル セキュリティ ポリシーを使用する](#use-local-security-policy)か、[レジストリを直接更新します](#update-the-registry)。

#### <a name="use-local-security-policy"></a>ローカル セキュリティ ポリシーを使用する

ローカル セキュリティ ポリシーを使用して LAN Manager 認証レベルを変更するには、次の手順を実行します。
 
1. [ローカル セキュリティ ポリシー] を開くには、 **[スタート]** 画面で「`secpol.msc`」と入力して、Enter キーを押します。

1. **[ローカル ポリシー]**  >  **[セキュリティ オプション]** に移動し、 **[ネットワーク セキュリティ: LAN Manager 認証レベル]** を開きます。

    ![[ローカル セキュリティ ポリシー] エディターの [セキュリティ オプション] が表示されているスクリーンショット。 [ネットワーク セキュリティ: LAN Manager 認証レベル] ポリシーが強調表示されています。](media/data-box-troubleshoot-share-access/security-policy-01.png)

1. 設定を **[NTLMv2 応答のみ送信 (LM と NTLM を拒否する)]** に変更します。

    ![[ローカル セキュリティ ポリシー] エディターの [ネットワーク セキュリティ: LAN Manager 認証レベル] ポリシーが表示されているスクリーンショット。 NTLMv2 応答のみ送信 (LM と NTLM を拒否する) オプションが強調表示されています。](media/data-box-troubleshoot-share-access/security-policy-02.png)

#### <a name="update-the-registry"></a>レジストリを更新する

ローカル セキュリティ ポリシーで LAN Manager 認証レベルを変更できない場合は、レジストリを直接更新します。

レジストリを直接更新するには、次の手順を実行します。

1. レジストリ エディター (regedit32.exe) を開くには、 **[スタート]** 画面で「`regedt32`」と入力して、Enter キーを押します。

1. HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Control > LSA に移動します。

    ![LSA フォルダーが強調表示されているレジストリ エディターを示すスクリーンショット。](media/data-box-troubleshoot-share-access/security-policy-03.png)

1. LSA フォルダーで、LMCompatibilityLevel レジストリ キーを開いて、その値を 5 に変更します。

    ![レジストリの LmcompatibilityLevel キーを変更するために使用されるダイアログ ボックスのスクリーンショット。 [値のデータ] フィールドが強調表示されています。](media/data-box-troubleshoot-share-access/security-policy-04.png)

1. コンピューターを再起動してレジストリの変更を有効にします。

## <a name="next-steps"></a>次のステップ

- [SMB 経由でのデータ コピー](data-box-deploy-copy-data.md)。
- [Data Box でのデータのコピーに関する問題のトラブルシューティング](data-box-troubleshoot.md)。
- [Microsoft サポートに問い合わせる](data-box-disk-contact-microsoft-support.md)。
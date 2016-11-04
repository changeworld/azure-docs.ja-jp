---
title: Windows 8.1 ドメイン参加済みデバイスの自動デバイス登録の構成 | Microsoft Docs
description: " 自動的に Azure AD に登録する Windows 8.1 ドメイン参加済みデバイスを構成する手順 "
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: Markvi

---
# <a name="configure-automatic-device-registration-for-windows-8.1-domain-joined-devices"></a>Windows 8.1 ドメイン参加済みデバイスの自動デバイス登録の構成
Active Directory グループ ポリシーを使用すると、自動的に Azure AD に登録する Windows 8.1 ドメイン参加済みデバイスを構成できます。 グループ ポリシーを構成するには、グループ ポリシーの管理機能がインストールされたドメイン参加済みの Windows Server 2012 R2 または Windows 8.1 コンピューターが 1 つ以上必要です。 このグループ ポリシーがドメインに対して有効になると、コンピューターにログインするすべてのドメイン ユーザーが Azure AD のデバイス オブジェクトを使用して自動的かつ暗黙的に登録されます。 Azure AD には、物理デバイスの登録ユーザーごとに 1 つのデバイス オブジェクトが作成されます。「Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録」の前提条件を確認し、それらが満たされているようにしてください。

> [!NOTE]
> デバイスの自動登録をセットアップする最新手順については、「 [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。
> 
> 

## <a name="configure-the-group-policy-for-your-windows-8.1-domain-joined-devices"></a>Windows 8.1 ドメイン参加済みデバイスのグループ ポリシーの構成
1. サーバー マネージャーを開き、**[ツール]**  >  **[グループ ポリシーの管理]** に移動します。
2. [グループ ポリシーの管理] から、 **自動社内参加**を有効にするドメインに対応するドメインのノードに移動します。
3. **[グループ ポリシー オブジェクト]** を右クリックし、**[新規]** を選択します。 グループ ポリシー オブジェクトに **Automatic Workplace Join**などの名前を付けます。 **[OK]**をクリックします。
4. 新しいグループ ポリシー オブジェクトを右クリックし、 **[編集]**を選択します。
5. **[コンピューターの構成]**  >  **[ポリシー]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[社内参加]** の順に移動します。
6. [クライアント コンピューターを自動的に社内参加する] を右クリックして **[編集]**を選択します。
7. [有効] を選択し、[適用] をクリックします。 **[OK]**をクリックします。
8. これで、グループ ポリシー オブジェクトを選択した場所にリンクできるようになりました。 組織内のすべての Windows 8.1 ドメイン参加済みデバイスに対してこのポリシーを有効にするには、グループ ポリシーをドメインにリンクします。

## <a name="unregistering-your-windows-8.1-domain-joined-devices"></a>Windows 8.1 ドメイン参加済みデバイスの登録解除
ドメイン参加済みの Windows 8.1 デバイスの登録を解除するには、前のセクションで作成した社内参加グループ ポリシーの設定を変更します。 [クライアント コンピューターを自動的に社内参加する] のポリシーを無効に設定します。 こうすることで、新しいデバイスが自動的に社内参加することがなくなります。

既存のドメイン参加済みの Windows 8.1 マシンの登録を解除するには、次の 2 つのオプションのいずれかに従います。

* オプション 1: **PC 設定を使用して、Windows 8.1 ドメイン参加済みデバイスを登録解除する**
  
  1. Windows 8.1 デバイスで、**[PC 設定]**  >  **[ネットワーク]**  >  **[社内]** の順に移動します
  2. **[参加をやめる]**を選択します。
     このプロセスは、マシンにサインインしている、自動的に社内参加したドメイン ユーザーごとに繰り返す必要があります。
* オプション 2: スクリプトを使用して、Windows 8.1 ドメイン参加済みデバイスを登録解除する
  
  1. Windows 8.1 マシンでコマンド プロンプトを開き、次のコマンドを実行します。` %SystemRoot%\System32\AutoWorkplace.exe leave`

このコマンドは、マシンにサインインしている各ドメイン ユーザーのコンテキストで実行する必要があります。

## <a name="event-viewer-&-errors-for-windows-8.1-domain-joined-devices"></a>Windows 8.1 ドメイン参加済みデバイス用のイベント ビューアーとエラー
Windows 8.1 コンピューターの Windows イベント ログには、デバイスの登録に関連するメッセージが表示されます。 成功したイベントと失敗したイベントの両方のメッセージが表示されます。 

イベント ログは、イベント ビューアーの [アプリケーションとサービス] の **[ログ]**  >  **[Microsoft]**  >  **[Windows] > [社内参加]** にあります。

## <a name="additional-details"></a>追加情報
グループ ポリシーにより、ユーザーのコンテキストで実行されてユーザーのサインインによってトリガーされる、システムのスケジュールされたタスクが有効になります。 このタスクにより、サインインの完了後に、ユーザーとデバイスがサイレント モードで Azure AD に登録されます。 スケジュールされたタスクは、Windows 8.1 デバイスのタスク スケジューラ ライブラリの **[Microsoft]**  >  **[Windows]**  >  **[社内参加]** にあります。 タスクが実行され、サインインするすべての Active Directory ユーザーが登録されます。 

## <a name="additional-topics"></a>関連トピック
* [Azure Active Directory Device Registration の概要](active-directory-conditional-access-device-registration-overview.md)
* [Azure Active Directory への Windows 10 ドメイン参加済みデバイスの自動デバイス登録](active-directory-conditional-access-automatic-device-registration.md)
* [Windows 7 ドメイン参加済みデバイスの自動デバイス登録の構成](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!--HONumber=Oct16_HO2-->



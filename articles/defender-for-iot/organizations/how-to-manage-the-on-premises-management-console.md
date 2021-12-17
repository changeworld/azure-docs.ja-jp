---
title: オンプレミスの管理コンソールを管理する
description: バックアップと復元、ホスト名の定義、センサーへのプロキシの設定など、オンプレミスの管理コンソールのオプションについて説明します。
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 3604b4674a86d719f2fecf841347c0a55f5b9757
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339708"
---
# <a name="manage-the-on-premises-management-console"></a>オンプレミスの管理コンソールを管理する

この記事では、バックアップと復元、コミット済みデバイスのアクティブ化ファイルのダウンロード、証明書の更新、センサーへのプロキシの設定など、オンプレミスの管理コンソールのオプションについて説明します。

Azure portal からオンプレミスの管理コンソールをオンボードします。

## <a name="upload-an-activation-file"></a>アクティブ化ファイルをアップロードする

初めてサインインすると、オンプレミスの管理コンソールのアクティブ化ファイルがダウンロードされます。 このファイルには、オンボード プロセス中に定義された、コミット済みのデバイスの集合が含まれます。 この一覧には、複数のサブスクリプションに関連付けられているセンサーが含まれます。

最初にアクティブ化した後に、監視するデバイスの数が、オンボード中に定義されたコミット済みのデバイスの数を超えることがあります。 このイベントは、たとえば、管理コンソールにより多くのセンサーを接続した場合に発生する可能性があります。 監視するデバイスの数とコミット済みデバイスの数が一致しない場合は、管理コンソールに警告が表示されます。 このイベントが発生した場合は、新しいアクティブ化ファイルをアップロードする必要があります。

**アクティブ化ファイルをアップロードするには:**

1. Microsoft Defender for IoT の **[価格]** ページに移動します。
1. **[管理コンソールのアクティブ化ファイルのダウンロード]** タブを選択します。アクティブ化ファイルがダウンロードされます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="アクティブ化ファイルをダウンロードします。":::

1. 管理コンソールで **[システム設定]** を選択します。
1. **[アクティブ化]** を選択します。
1. **[ファイルの選択]** を選択し、保存したファイルを選択します。

## <a name="manage-certificates"></a>証明書の管理

オンプレミス管理コンソールのインストール後に、ローカルの自己署名証明書が生成され、Web アプリケーションにアクセスするために使用されます。 管理者ユーザーは、オンプレミスの管理コンソールに初めてログインするときに、SSL/TLS 証明書を指定するように求められます。 

管理者には、最初のログイン後にアップロードされた証明書の更新が求められることがあります。 これは証明書の有効期限が切れた場合などに発生することがあります。

**証明書を更新するには:**

1. **[システム設定]** を選択します。

1. **[SSL/TLS 証明書]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="証明書のアップロード":::

1. [SSL/TLS 証明書] ダイアログ ボックスで、既存の証明書を削除し、新しい証明書を追加します。

    - 証明書名を追加します。
    - CRT ファイルとキー ファイルをアップロードします。
    - 必要に応じて PEM ファイルをアップロードします。

アップロードに失敗した場合、セキュリティ管理者または IT 管理者に問い合わせるか、「[証明書について](how-to-deploy-certificates.md)」の情報をご確認ください。

**証明書の検証設定を変更するには:**

1. **[証明書の検証を有効にする]** トグルを有効または無効にします。 オプションが有効になっていて、検証に失敗した場合は、関連コンポーネント間の通信が停止し、検証エラーがコンソールに表示されます。 無効にした場合、証明書検証は実行されません。詳細については、「[証明書の検証について](how-to-deploy-certificates.md#about-certificate-validation)」を参照してください。

1. **[保存]** を選択します。

初回の証明書のアップロードの詳細については、「[初回サインインとアクティブ化のチェックリスト](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist)」を参照してください

## <a name="define-backup-and-restore-settings"></a>バックアップと復元の設定を定義する

オンプレミスの管理コンソールのシステム バックアップが毎日自動的に実行されます。 データは別のディスクに保存されます。 既定の場所は `/var/cyberx/backups` です。 

このファイルを内部ネットワークに自動的に転送できます。 

> [!NOTE]
> バックアップと復元の手順は、同じバージョンでのみ実行できます。 

オンプレミスの管理コンソール マシンをバックアップするには: 

- 管理アカウントにサインインし、「`sudo cyberx-management-backup -full`」と入力します。

最新のバックアップ ファイルを復元するには:

- 管理者アカウントにサインインし、「`$ sudo cyberx-management-system-restore`」と入力します。

外部の SMB サーバーにバックアップを保存するには:

1. 外部の SMB サーバーに共有フォルダーを作成します。  

   SMB サーバーへのアクセスに必要なフォルダー パス、ユーザー名、パスワードを取得します。 

2. Defender for IoT で、バックアップ用のディレクトリを作成します。

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. fstab を編集します。  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. 共有する SMB サーバーの資格情報を編集または作成します。 

   - `sudo nano /etc/samba/user` 

5. 次の項目を追加します。 

   - `username=<user name>`

   - `password=<password>` 

6. ディレクトリをマウントします。 

   - `sudo mount -a` 

7. Defender for IoT オンプレミス管理コンソールの共有フォルダーにバックアップ ディレクトリを構成します。  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>ホスト名を編集する

組織の DNS サーバーで構成されている管理コンソールのホスト名を編集するには:

1. 管理コンソールの左ペインで **[システム設定]** を選択します。  

2. コンソールのネットワーク セクションで、 **[ネットワーク]** を選択します。 

3. 組織の DNS サーバーで構成されているホスト名を入力します。 

4. **[保存]** を選択します。

## <a name="define-vlan-names"></a>VLAN 名を定義する

VLAN 名は、センサーと管理コンソールの間で同期されません。 コンポーネントで同じ名前を定義します。

ネットワーク領域で **[VLAN]** を選択し、検出された VLAN ID に名前を追加します。 次に、 **[保存]** を選択します。

## <a name="define-a-proxy-to-sensors"></a>センサーへのプロキシを定義する

ユーザーがセンサーに直接サインインできないようにすることで、システムのセキュリティを強化します。 代わりに、プロキシ トンネリングを使用して、ユーザーが 1 つのファイアウォール規則でオンプレミスの管理コンソールからセンサーにアクセスできるようにします。 この機能強化により、センサーを介してネットワーク環境に不正にアクセスされる可能性が減少します。

センサーへの直接接続がない環境では、プロキシを使用します。

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="ユーザー。":::

次の手順では、センサーをオンプレミスの管理コンソールに接続し、そのコンソールでトンネリングを有効にします。

1. 管理者資格情報を使用して、オンプレミスの管理コンソール アプライアンスの CLI にサインインします。

2. 「`sudo cyberx-management-tunnel-enable`」と入力し、**Enter** キーを押します。

4. 「`--port 10000`」と入力し、**Enter** キーを押します。

## <a name="adjust-system-properties"></a>システム プロパティを調整する

システム プロパティにより、管理コンソールのさまざまな操作と設定が制御されます。 それらを編集または変更すると、管理コンソールの操作に悪影響を及ぼす可能性があります。 設定を変更する前に、[Microsoft サポート](https://support.microsoft.com)にお問い合わせください。

システム プロパティにアクセスするには: 

1. オンプレミス管理コンソールまたはセンサーにサインインします。

2. **[システム設定]** を選択します。

3. **[全般]** セクションで **[システム プロパティ]** を選択します。

## <a name="change-the-name-of-the-on-premises-management-console"></a>オンプレミスの管理コンソールの名前を変更する

オンプレミスの管理コンソールの名前を変更できます。 コンソール Web ブラウザー、さまざまなコンソール ウィンドウ、トラブルシューティング ログに新しい名前が表示されます。 既定の名前は **管理コンソール** です。

名前を変更するには:

1. 左ペインの下部で、現在の名前を選択します。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="オンプレミスの管理コンソール バージョンのスクリーンショット。":::

2. **[Edit management console configuration]\(管理コンソールの構成の編集\)** ダイアログ ボックスで、新しい名前を入力します。 名前は 25 文字以下にする必要があります。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Defender for IoT プラットフォーム構成の編集のスクリーンショット。":::

3. **[保存]** を選択します。 新しい名前が適用されます。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="変更されたコンソール名を示すスクリーンショット。":::

## <a name="password-recovery"></a>パスワードの回復

オンプレミスの管理コンソールのパスワードの回復は、デバイスがアタッチされているサブスクリプションに関連付けられています。 デバイスがアタッチされているサブスクリプションがわからない場合、パスワードを回復することはできません。

パスワードをリセットするには:

1. オンプレミスの管理コンソールのサインイン ページに移動します。
1. **[パスワードの回復]** を選択します。
1. 一意識別子をコピーします。
1. Defender for IoT の **[サイトとセンサー]** ページに移動し、 **[パスワードの回復]** タブを選択します。
1. 一意識別子を入力し、 **[回復]** を選択します。 アクティブ化ファイルがダウンロードされます。
1. **[パスワードの回復]** ページに移動し、アクティブ化ファイルをアップロードします。
1. **[次へ]** を選択します。
 
   これで、ユーザー名と、システムによって生成された新しいパスワードが付与されます。

> [!NOTE]
> センサーは、最初に接続されていたサブスクリプションにリンクされます。 パスワードは、アタッチ先のサブスクリプションと同じものを使用してのみ回復できます。

## <a name="update-the-software-version"></a>ソフトウェアのバージョンを更新する

次の手順では、オンプレミス管理コンソール ソフトウェアのバージョンを更新する方法について説明します。 更新プロセスには約 30 分かかります。

オンプレミスの管理コンソールと管理対象センサーを使用している場合は、**まず管理コンソールを更新します**。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

1. Defender for IoT にアクセスします。

1. **[更新プログラム]** ページに移動します。

1. オンプレミス管理コンソール セクションからバージョンを選択します。

1. **[Download]\(ダウンロード\)** を選択してファイルを保存します。

1. オンプレミス管理コンソールにログインし、サイド メニューから **[システム設定]** を選択します。

1. **[バージョンのアップグレード]** ペインで、 **[アップグレード]** を選択します。

1. Defender for IoT の **[更新プログラム]** ページからダウンロードしたファイルを選択します。

## <a name="mail-server-settings"></a>メール サーバーの設定

オンプレミス管理コンソールの SMTP メールサーバー設定を定義します。

定義するには:

1. 管理者資格情報を使用して、オンプレミス管理用の CLI にサインインします。
1. 「```nano /var/cyberx/properties/remote-interfaces.properties```」と入力します。
1. [Enter] を選択します。 次のプロンプトが表示されます。
   `mail.smtp_server=`
   `mail.port=25`
   `mail.sender=`
1. SMTP サーバーの名前と送信者を入力し、Enter キーを押します。

## <a name="see-also"></a>関連項目

[管理コンソールからセンサーを管理する](how-to-manage-sensors-from-the-on-premises-management-console.md)

[個々のセンサーの管理](how-to-manage-individual-sensors.md)

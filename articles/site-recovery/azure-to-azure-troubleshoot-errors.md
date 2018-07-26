---
title: Azure 間レプリケーションの問題とエラーに関する Azure Site Recovery トラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするとき発生するエラーや問題に関するトラブルシューティング
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: sujayt
ms.openlocfilehash: a41cd658060ef92efb0fc21a98ca616276378c5e
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113856"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 間の VM レプリケーションに関する問題のトラブルシューティング

この記事では、リージョン間で Azure 仮想マシンをレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的な問題と、そのトラブルシューティング方法について説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)に関するページをご覧ください。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure リソースのクォータに関する問題 (エラー コード 150097)
ディザスター リカバリー リージョンとして使用するターゲット リージョンで Azure VM を作成するには、サブスクリプションを有効にする必要があります。 また、特定のサイズの VM を作成するには、サブスクリプションで十分なクォータが有効になっている必要もあります。 Site Recovery では、既定で、ソース VM と同じサイズがターゲット VM に対して選択されます。 同じサイズを使用できない場合は、最も近いサイズが自動的に取得されます。 このエラー メッセージは、ソース VM 構成をサポートするサイズと一致するものがない場合に表示されます。

**エラー コード** | **考えられる原因** | **推奨事項**
--- | --- | ---
150097<br></br>**メッセージ**: 仮想マシン VmName のレプリケーションを有効にできませんでした。 | - ターゲット リージョンの場所で VM を作成するための、サブスクリプション ID が有効になっていない可能性があります。</br></br>- ターゲット リージョンの場所で特定の VM サイズを作成するための、サブスクリプション ID が有効になっていない可能性があります。または、十分なクォータが確保されていません。</br></br>- ターゲット リージョンの場所のサブスクリプション ID について、ソース VM の NIC 数 (2) と一致する適切なターゲットVM サイズが見つかりません。| [Azure 課金のサポート](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)に連絡して、サブスクリプションのターゲットの場所で、必要な VM サイズの VM 作成を有効にしてください。 有効にした後、失敗した操作を再試行してください。

### <a name="fix-the-problem"></a>問題の解決
[Azure 課金のサポート](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)に連絡して、サブスクリプションが、必要なサイズの VM をターゲットの場所で作成できるようにしてください。

ターゲットの場所に容量の制約がある場合は、レプリケーションを無効にしてから、必要なサイズの VM を作成できるだけのクォータが確保されているサブスクリプションがある別の場所へのレプリケーションを有効にします。

## <a name="trusted-root-certificates-error-code-151066"></a>信頼されたルート証明書 (エラー コード 151066)

すべての最新の信頼されたルート証明書が VMにない場合、"レプリケーションの有効化" ジョブが失敗することがあります。 証明書がないと、VM からの Site Recovery サービス呼び出しの認証と承認が失敗します。 失敗した "レプリケーションの有効化" Site Recovery ジョブのエラー メッセージが表示されます。

**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
151066<br></br>**メッセージ**: Site Recovery の構成に失敗しました。 | 承認と認証に使用される、必要な信頼されたルート証明書がマシンに存在しません。 | - Windows オペレーティング システムを実行している VM については、信頼されたルート証明書がマシンに存在することを確認します。 詳細については、「[信頼されたルートおよび許可されない証明書を構成する](https://technet.microsoft.com/library/dn265983.aspx)」を参照してください。<br></br>- Linux オペレーティング システムを実行している VM については、Linux オペレーティング システム バージョンのディストリビューターによって発行された、信頼されたルート証明書のガイダンスに従ってください。

### <a name="fix-the-problem"></a>問題の解決
**Windows**

すべての最新 Windows 更新プログラムを VM にインストールし、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows 更新プロセスに従って、証明書を取得してください。 VM に必要な証明書がない場合は、セキュリティ上の理由から、Site Recovery サービスへの呼び出しが失敗します。

組織の一般的な Windows 更新管理プロセスまたは証明書更新管理プロセスに従って、VM ですべての最新ルート証明書と更新済み証明書失効リストを取得します。

問題が解決されたことを確認するには、VM のブラウザーから login.microsoftonline.com にアクセスします。

**Linux**

VM の最新の信頼されたルート証明書と最新の証明書失効リストを取得するには、Linux ディストリビューターによって提供されるガイダンスに従います。

SuSE Linux ではシンボリック リンクを使用して証明書リストを保持しているため、次の手順に従います。

1.  ルート ユーザーとしてサインインします。

2.  次のコマンドを実行してディレクトリを変更します。

      ``# cd /etc/ssl/certs``

3. Symantec ルート CA 証明書が存在するかどうかを確認します。

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Symantec ルート CA 証明書が見つからない場合は、次のコマンドを実行してファイルをダウンロードします。 エラーがないか確認し、ネットワーク障害に対する推奨アクションに従います。

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Baltimore ルート CA 証明書が存在するかどうかを確認します。

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Baltimore ルート CA 証明書が見つからない場合は、証明書をダウンロードします。  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. DigiCert_Global_Root_CA 証明書が存在するかどうかを確認します。

    ``# ls DigiCert_Global_Root_CA.pem``

8. DigiCert_Global_Root_CA が見つからない場合は、次のコマンドを実行して証明書をダウンロードします。

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. 再ハッシュ スクリプトを実行して、新たにダウンロードした証明書のサブジェクト ハッシュを更新します。

    ``# c_rehash``

10. シンボリック リンクとしてのサブジェクト ハッシュが証明書用に作成されているかどうかを確認します。

    - コマンド

      ``# ls -l | grep Baltimore``

    - 出力

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - コマンド

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - 出力

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - コマンド

      ``# ls -l | grep DigiCert_Global_Root``

    - 出力

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

11. ファイル VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem のコピーを b204d74a.0 というファイル名で作成します。

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. ファイル Baltimore_CyberTrust_Root.pem のコピーを 653b494a.0 というファイル名で作成します。

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. ファイル DigiCert_Global_Root_CA.pem のコピーを 3513523f.0 というファイル名で作成します。

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. これらのファイルが存在するかどうかを確認します。  

    - コマンド

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - 出力

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL または IP 範囲の送信接続 (エラー コード 151037 または 151072)

Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかのエラー メッセージが表示される可能性があります。

**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
151037<br></br>**メッセージ**: Azure 仮想マシンを Site Recovery に登録できませんでした。 | - NSG を使用して VM で送信アクセスを制御していますが、送信アクセスに必要な IP 範囲がホワイトリストに登録されていません。</br></br>- サード パーティのファイアウォール ツールを使用していますが、必要な IP 範囲/URL がホワイトリストに登録されていません。</br>| - ファイアウォール プロキシを使用して VM で送信ネットワーク接続を制御している場合は、前提条件となる URL またはデータセンターの IP 範囲がホワイトリストに登録されていることを確認します。 詳細については、[ファイアウォール プロキシのガイダンス](https://aka.ms/a2a-firewall-proxy-guidance)を参照してください。</br></br>- NSG ルールを使用して VM で送信ネットワーク接続を制御している場合は、前提条件となるデータセンターの IP 範囲がホワイトリストに登録されていることを確認します。 詳細については、[ネットワーク セキュリティ グループのガイダンス](https://aka.ms/a2a-nsg-guidance)を参照してください。
151072<br></br>**メッセージ**: Site Recovery の構成に失敗しました。 | Site Recovery サービスのエンドポイントに対する接続を確立できません。 | - ファイアウォール プロキシを使用して VM で送信ネットワーク接続を制御している場合は、前提条件となる URL またはデータセンターの IP 範囲がホワイトリストに登録されていることを確認します。 詳細については、[ファイアウォール プロキシのガイダンス](https://aka.ms/a2a-firewall-proxy-guidance)を参照してください。</br></br>- NSG ルールを使用して VM で送信ネットワーク接続を制御している場合は、前提条件となるデータセンターの IP 範囲がホワイトリストに登録されていることを確認します。 詳細については、[ネットワーク セキュリティ グループのガイダンス](https://aka.ms/a2a-nsg-guidance)を参照してください。

### <a name="fix-the-problem"></a>問題の解決
[必要な URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) または [必要な IP 範囲](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)をホワイトリストに登録するには、[ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)の次の手順に従ってください。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>ディスクがマシンに見つかりません (エラー コード 150039)

VM に接続された新しいディスクを初期化する必要があります。

**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
150039<br></br>**メッセージ**: 論理ユニット番号 (LUN) が (LUNValue) の Azure データ ディスク (DiskName) (DiskURI) が、VM 内部から報告されている同じ LUN 値のディスクにマップされませんでした。 | - 新しいデータ ディスクが VM に接続されているが、初期化されませんでした。</br></br>- VM 内部のデータ ディスクは、そのディスクが VM に接続されている LUN 値を正しく報告していません。| データ ディスクが初期化されていることを確認し、操作を再試行します。</br></br>Windows の場合: [新しいディスクを接続して初期化する](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。</br></br>Linux の場合: [Linux で新しいデータ ディスクを初期化する](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

### <a name="fix-the-problem"></a>問題の解決
データ ディスクが初期化されていることを確認し、操作を再試行します。

- Windows の場合: [新しいディスクを接続して初期化する](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。
- Linux の場合: [Linux で新しいデータ ディスクを追加する](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

問題が解決しない場合は、サポートにお問い合わせください。


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>"レプリケーションの有効化" で選択する Azure VM を表示できない

 **原因 1: リソース グループとソース仮想マシンが別々の場所にある** <br>
Azure Site Recovery では現在、ソース リージョンのリソース グループと仮想マシンが同じ場所にあることが必須となっています。 そうでない場合、保護されている間に仮想マシンを見つけることができません。

**原因 2: このリソース グループが、選択されたサブスクリプションの一部でない** <br>
特定のサブスクリプションの一部ではない場合、保護されたときにリソース グループを見つけられない可能性があります。 リソース グループが、使用されているサブスクリプションに属していることを確認します。

 **原因 3: 構成が古い** <br>
レプリケーションを有効にする VM が表示されない場合は、Azure VM 上に古い Site Recovery 構成が残されていることが原因である可能性があります。 古い構成は、次の場合に Azure VM に残る可能性があります。

- Site Recovery を使用して Azure VM のレプリケーションを有効にし、VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- Site Recovery を使用して Azure VM のレプリケーションを有効にし、VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。

### <a name="fix-the-problem"></a>問題の解決

[古い ASR 構成を削除するスクリプト](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412)を使用して、Azure VM で古い Site Recovery 構成を削除できます。 古い構成を削除すると、VM が表示されます。

## <a name="unable-to-select-virtual-machine-for-protection"></a>保護対象の仮想マシンを選択できない 
 **原因 1: 仮想マシンの何らかの拡張機能のインストールが失敗したかまたは応答しない状態にある** <br>
 [仮想マシン]、[設定]、[拡張機能] の順に進み、拡張機能のいずれかが失敗した状態になっていないか確認します。 失敗した拡張機能をアンインストールして、仮想マシンの保護を再試行します。<br>
 **原因 2: [VM のプロビジョニングの状態が有効ではありません](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>VM のプロビジョニングの状態が正しくありません (エラー コード 150019)

VM でレプリケーションを有効にするには、プロビジョニングの状態が **[成功]** になっている必要があります。 次の手順に従って、VM の状態を確認できます。

1.  Azure Portal の **[All Services]\(すべてのサービス\)** から **[リソース エクスプローラー]** を選択します。
2.  **[サブスクリプション]** 一覧を展開して、自分のサブスクリプションを選択します。
3.  **[ResourceGroups]** 一覧を展開して、VM のリソース グループを選択します。
4.  **[リソース]** 一覧を展開して、お使いの仮想マシンを選択します。
5.  右側にあるインスタンス ビューで、**[provisioningState]** フィールドを確認します。

### <a name="fix-the-problem"></a>問題の解決

- **[provisioningState]** が **[失敗]** になっている場合は、サポートに詳細を問い合わせてトラブルシューティングします。
- **[provisioningState]** が **[更新中]** になっている場合は、別の拡張機能がデプロイされている可能性があります。 VM 上に何らかの進行中の操作があるかを確認し、それらの操作が完了するまで待機してから、失敗した Site Recovery の **[レプリケーションを有効にする]** ジョブを再試行します。



## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/ボリューム シャドウ コピー サービス エラー (エラー コード 151025)
**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
151025<br></br>**メッセージ**: サイト回復拡張機能をインストールできませんでした | - COM+ システム アプリケーション サービスが無効になっています。</br></br>- ボリューム シャドウ コピー サービスが無効になっています。| COM+ システム アプリケーション サービスとボリューム シャドウ コピー サービスを、自動または手動スタートアップ モードに設定します。

### <a name="fix-the-problem"></a>問題の解決

[サービス] コンソールを開いて、COM+ システム アプリケーションとボリューム シャドウ コピーの [スタートアップの種類] が [無効] に設定されていないことを確認します。
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="next-steps"></a>次の手順
[Azure 仮想マシンのレプリケート](site-recovery-replicate-azure-to-azure.md)

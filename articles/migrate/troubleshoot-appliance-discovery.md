---
title: Azure Migrate アプライアンスのデプロイと検出のトラブルシューティング
description: Azure Migrate アプライアンスのデプロイとマシンの検出に関するヘルプを表示します。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 3f3604205d4aedffdda128ec4a6b895786245e56
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772026"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Azure Migrate アプライアンスと検出のトラブルシューティング

この記事は、[Azure Migrate](migrate-services-overview.md) アプライアンスをデプロイし、そのアプライアンスを使用してオンプレミスのマシンを検出する際の問題のトラブルシューティングに役立ちます。


## <a name="whats-supported"></a>サポート対象

アプライアンスのサポート要件を[確認](migrate-appliance.md)します。


## <a name="invalid-ovf-manifest-entry"></a>"Invalid OVF manifest entry (無効な OVF マニフェスト エントリ)"

"The provided manifest file is invalid: Invalid OVF manifest entry" (指定されたマニフェスト ファイルが無効です: 無効な OVF マニフェスト エントリ) というエラーを受け取る場合は、次のようにします。

1. Azure Migrate アプライアンスの OVA ファイルが正常にダウンロードされていることを、そのハッシュ値をチェックして確認します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 ハッシュ値が一致しない場合は、OVA ファイルをもう一度ダウンロードしてデプロイを再試行してください。
2. それでもデプロイが失敗する場合で、VMware vSphere クライアントを使って OVF ファイルをデプロイしている場合は、vSphere Web クライアントでデプロイしてみてください。 展開がそれでも失敗する場合は、別の Web ブラウザーをお試しください。
3. vSphere Web クライアントを使用しており、vCenter Server 6.5 または 6.7 にそれをデプロイしようとしている場合は、ESXi ホストに直接 OVA をデプロイしてみてください。
   - Web クライアント (https://<*ホスト IP アドレス*>/ui) を使用して (vCenter Server ではなく) ESXi ホストに直接接続します。
   - **[ホーム]**  >  **[インベントリ]** で、 **[ファイル]**  >  **[Deploy OVF template]\(OVF テンプレートのデプロイ\)** を選択します。 OVA を参照して、デプロイを完了する。
4. それでもデプロイが失敗する場合は、Azure Migrate のサポートにお問い合わせください。

## <a name="cant-connect-to-the-internet"></a>インターネットに接続できない

これは、アプライアンス マシンがプロキシの内側にある場合に発生する可能性があります。

- プロキシに承認資格情報が必要な場合は、それを提供します。
- URL ベースのファイアウォール プロキシを使用して送信接続を制御している場合は、以下の URL を許可リストに追加します。

    - [VMware 評価のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)
    - [Hyper-V 評価のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)
    - [VMware のエージェントレスの移行のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)
    - [VMware のエージェント ベースの移行のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)
    - [Hyper-V の移行のための URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)

- インターネットへの接続にインターセプト プロキシを使用している場合は、[こちらの手順](https://docs.microsoft.com/azure/migrate/concepts-collector)を使用して、プロキシの証明書をアプライアンス VM にインポートします。

##  <a name="datetime-synchronization-error"></a>日付と時刻の同期エラー

日付と時刻の同期に関するエラー (802) は、サーバー クロックが現在の時刻と同期しておらず、5 分以上ずれている可能性があることを示します。 コレクター VM のクロックの時刻を変更して、現在の時刻と一致させます。

1. VM で管理者用のコマンド プロンプトを開きます。
2. タイム ゾーンを確認するには、**w32tm /tz** を実行します。
3. 時刻を同期するには、**w32tm /resync** を実行します。


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

この接続エラーを受け取る場合、vCenter Server <*サーバー名*>.com:9443 に接続できない可能性があります。 エラーの詳細では、 https://<*サーバー名*>.com:9443/sdk でリッスンしているエンドポイントで、メッセージを受け入れることができるものがないことが示されています。

- 最新バージョンのアプライアンスを実行しているかどうかを確認します。 そうでない場合は、アプライアンスを[最新バージョン](https://docs.microsoft.com/azure/migrate/concepts-collector)にアップグレードします。
- 最新バージョンでも引き続き問題が発生する場合は、指定された vCenter Server 名をアプライアンスで解決できないか、または指定されたポートが間違っている可能性があります。 ポートが指定されていない場合、コレクターでは既定でポート番号 443 への接続が試みられます。

    1. アプライアンスから <*サーバー名*>.com に対して ping を実行します。
    2. ステップ 1 が失敗する場合は、IP アドレスを使用して vCenter サーバーへの接続を試みます。
    3. vCenter Server に接続するための正しいポート番号を確認します。
    4. vCenter Server が起動されて実行中であることを確認します。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>エラー 60052/60039: アプライアンスが登録されていない可能性がある

- エラー 60052 "The appliance might not be registered successfully to the Azure Migrate project" (アプライアンスが Azure Migrate プロジェクトに正常に登録されていない可能性があります) は、アプライアンスの登録に使用された Azure アカウントに十分な権限がない場合に発生します。
    - アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。
    - 必要な Azure ロールとアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)してください。
- エラー 60039 "The appliance might not be registered successfully to the Azure Migrate project" (アプライアンスが Azure Migrate プロジェクトに正常に登録されていない可能性があります) は、アプライアンスの登録に使用された Azure Migrate プロジェクトが見つからない場合に発生する可能性があります。
    - Azure portal で、リソース グループにプロジェクトが存在するかどうかを確認してください。
    - プロジェクトが存在しない場合は、ご利用のリソース グループに新しい Azure Migrate プロジェクトを作成して、アプライアンスをもう一度登録してください。 新しいプロジェクトを作成する[方法を参照](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)してください。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>エラー 60030/60031: Key Vault の管理操作が失敗した

"Azure Key Vault 管理操作に失敗しました" というエラー 60030 または 60031 を受け取る場合は、次のようにします。
- アプライアンスの登録に使用する Azure ユーザー アカウントに、少なくともサブスクリプションの共同作成者アクセス許可があることを確認します。
- エラー メッセージで指定されているキー コンテナーへのアクセス権がアカウントにあることを確認した後、操作を再試行します。
- 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。
- 必要な Azure ロールとアクセス許可の[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)してください。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>エラー 60028: 検出を開始できなかった

エラー 60028: "Discovery couldn't be initiated because of an error. The operation failed for the specified list of hosts or clusters" (エラーが発生したため、検出を開始できませんでした。指定されたホストまたはクラスターのリストに対する操作に失敗しました) は、VM 情報のアクセスまたは取得で問題が発生したため、エラーで示されているホストで検出を開始できなかったことを示します。 残りのホストは正常に追加されました。

- **[ホストの追加]** オプションを使用して、エラーで示されているホストをもう一度追加します。
- 検証エラーが発生している場合は、修復のガイダンスを確認してエラーを修正し、 **[保存して検出を開始]** オプションをもう一度試します。

## <a name="error-60025-azure-ad-operation-failed"></a>エラー 60025: Azure AD operation failed (Azure AD の操作が失敗しました) 
エラー 60025: "An Azure AD operation failed. The error occurred while creating or updating the Azure AD application" (Azure AD の操作が失敗しました。Azure AD アプリケーションを作成または更新しているときにエラーが発生しました) は、検出を開始するために使用された Azure ユーザー アカウントが、アプライアンスの登録に使用されたアカウントと異なる場合に発生します。 次のいずれかの操作を行います。

- 検出を開始するユーザー アカウントが、アプライアンスの登録に使用するものと同じであることを確認します。
- 検出操作が失敗しているユーザー アカウントに、Azure Active Directory アプリケーションのアクセス許可を提供します。
- Azure Migrate プロジェクト用に以前に作成したリソース グループを削除します。 別のリソース グループを作成して、もう一度開始します。
- Azure Active Directory アプリケーションのアクセス許可に関する[詳細を参照](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)してください。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>エラー 50004: ホストまたはクラスターに接続できない

エラー 50004: "Can't connect to a host or cluster because the server name can't be resolved. (サーバー名を解決できないため、ホストまたはクラスターに接続できません。) WinRM エラー コード: 0x803381B9" は、アプライアンスの要求を処理する Azure DNS で、指定されたクラスターまたはホストの名前を解決できない場合に発生する可能性があります。

- クラスターでこのエラーが発生する場合は、クラスターの FQDN。
- クラスター内のホストについても、このエラーが表示されることがあります。 これは、アプライアンスはクラスターに接続できますが、クラスターから FQDN ではないホスト名が返されることを示します。 このエラーを解決するには、IP アドレスとホスト名のマッピングを追加することでアプライアンス上の hosts ファイルを更新します。
    1. 管理者としてメモ帳を開きます。
    2. C:\Windows\System32\Drivers\etc\hosts ファイルを開きます。
    3. IP アドレスとホスト名を 1 行で追加します。 このエラーが見られるホストまたはクラスターごとに繰り返します。
    4. hosts ファイルを保存して閉じます。
    5. アプライアンス管理アプリを使用して、アプライアンスからホストに接続できるかどうかを確認します。 30 分後、これらのホストの最新情報が Azure portal に表示されるようになるはずです。

## <a name="discovered-vms-not-in-portal"></a>ポータルで VM が検出されない

検出状態は "Discovery in progress (検出中)" だが、ポータルに VM がまだ表示されない場合は、数分お待ちください。
- VMware VM では約 15 分かかります。
- Hyper-V VM 検出でホストが追加されるごとに、約 2 分かかります。

待っても状態が変わらない場合は、 **[サーバー]** タブで **[最新の情報に更新]** を選択してください。これにより、検出されたサーバーの数が Azure Migrate に表示されます。Server Assessment、Azure Migrate: Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。

これがうまくいかず、VMware サーバーを検出している場合は、次を実行してください。

- 指定した vCenter アカウントに、少なくとも 1 つの VM にアクセスできるアクセス許可が正しく設定されていることを確認してください。
- vCenter アカウントに VM フォルダー レベルでアクセス権が付与されている場合、Azure Migrate は VMware VM を検出できません。 検出のスコープ設定についての[詳細をご覧ください](tutorial-assess-vmware.md#set-the-scope-of-discovery)。

## <a name="vm-data-not-in-portal"></a>VM データがポータルにない

検出された VM がポータルに表示されないか、VM データが期限切れになっている場合は、数分待ってください。 検出された VM 構成データの変更内容がポータルに表示されるまでには最大で 30 分かかります。 アプリケーション データの変更が表示されるまでに数時間かかる場合もあります。 この時間が経過してもデータがない場合は、次のように最新の情報に更新してみてください

1. **[サーバー]**  >  **[Azure Migrate Server Assessment]\(Azure Migrate Server Assessment\)** で、 **[概要]** を選択します。
2. **[管理]** の下で、 **[Agent Health]\(エージェントの正常性\)** を選択します。
3. **[エージェントを更新する]** を選択します。
4. 更新操作が完了するまで待ちます。 これで、最新の情報が表示されるはずです。

## <a name="deleted-vms-appear-in-portal"></a>削除した VM がポータルに表示される

VM を削除してもまだポータルに表示されている場合は、30 分待ってください。 まだ表示されている場合は、前述の説明に従って最新の情報に更新してください。

## <a name="common-app-discovery-errors"></a>一般的なアプリ検出エラー

Azure Migrate は、Azure Migrate を使用してアプリケーション、ロール、および機能の検出をサポートします。Server Assessment を使用して作成する方法について説明します。 現在、アプリ検出は VMware でのみサポートされています。 アプリ検出を設定するための要件と手順についての[詳細をご覧ください](how-to-discover-applications.md)。

一般的なアプリ検出エラーを表にまとめています。

**Error** | **原因** | **操作**
--- | --- | --- | ---
10000: "Unable to discover the applications installed on the server".(サーバーにインストールされているアプリケーションを検出できません。) | これは、マシンのオペレーティング システムが Windows でも Linux でもない場合に発生する可能性があります。 | アプリ検出は、Windows/Linux にのみ使用してください。
10001: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | 内部エラー - アプライアンスでいくつかのファイルが不足しています。 | Microsoft サポートにお問い合わせください。
10002: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | アプライアンス上の検出エージェントが正常に動作していない可能性があります。 | 24 時間以内に問題が自動的に解決しない場合は、サポートにお問い合わせください。
10003: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | アプライアンス上の検出エージェントが正常に動作していない可能性があります。 | 24 時間以内に問題が自動的に解決しない場合は、サポートにお問い合わせください。
10004: "Unable to discover installed applications for <Windows/Linux> machines." (<Windows/Linux> マシンにインストールされているアプリケーションを検出できません。) |  <Windows/Linux> マシンにアクセスするための資格情報がアプライアンスで提供されていません。| <Windows/Linux> マシンにアクセスできる資格情報をアプライアンスに追加してください。
10005: "Unable to access the on-premises server". (オンプレミスのサーバーにアクセスできません。) | アクセス資格情報が間違っている可能性があります。 | アプライアンスの資格情報を更新し、それを使用して関連するマシンにアクセスできることを確認してください。 
10006: "Unable to access the on-premises server". (オンプレミスのサーバーにアクセスできません。) | これは、マシンのオペレーティング システムが Windows でも Linux でもない場合に発生する可能性があります。|  アプリ検出は、Windows/Linux にのみ使用してください。
9000/9001/9002: "Unable to discover the applications installed on the server".(サーバーにインストールされているアプリケーションを検出できません。) | VMware ツールがインストールされていないか、破損している可能性があります。 | 関連するマシンに VMware ツールをインストール/再インストールし、実行されていることを確認します。
9003: "Unable to discover the applications installed on the server".サーバーにインストールされているアプリケーションを検出できません。 | これは、マシンのオペレーティング システムが Windows でも Linux でもない場合に発生する可能性があります。 | アプリ検出は、Windows/Linux にのみ使用してください。
9004: "Unable to discover the applications installed on the server".(サーバーにインストールされているアプリケーションを検出できません。) | これは、VM の電源がオフになっている場合に発生する可能性があります。 | 検出を行うには、VM がオンになっていることを確認します。
9005: "Unable to discover the applications installed on the VM." (VM にインストールされているアプリケーションを検出できません。) | これは、マシンのオペレーティング システムが Windows でも Linux でもない場合に発生する可能性があります。 | アプリ検出は、Windows/Linux にのみ使用してください。
9006/9007: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | アプライアンス上の検出エージェントが正常に動作していない可能性があります。 | 24 時間以内に問題が自動的に解決しない場合は、サポートにお問い合わせください。
9008: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | 内部エラーの可能性があります。  | 24 時間以内に問題が自動的に解決しない場合は、サポートにお問い合わせください。
9009: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | サーバーの Windows ユーザー アカウント制御 (UAC) の設定の制限により、インストールされているアプリケーションの検出が妨げられている場合に発生します。 | サーバーで [ユーザーアカウント制御] の設定を探し、サーバーの UAC のレベル設定が下位の 2 つのいずれかになるように構成します。
9010: "Unable to retrieve the applications installed the server". (サーバーにインストールされているアプリケーションを取得できません。) | 内部エラーの可能性があります。  | 24 時間以内に問題が自動的に解決しない場合は、サポートにお問い合わせください。
8084: "Unable to discover applications due to VMware error: <Exception from VMware>" (次の VMware エラーのため、アプリケーションを検出できません:) | Azure Migrate アプライアンスは、アプリケーションの検出に VMware API を使用しています。 この問題は、アプリケーションを検出しようとしている時に vCenter Server から例外がスローされた場合に発生することがあります。 VMware からのエラー メッセージは、ポータルに表示されるエラー メッセージに表示されます。 | [VMware ドキュメント](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)でメッセージを検索し、修正手順に従ってください。 修正できない場合は、Microsoft サポートにお問い合わせください。
9012:"Unable to discover the applications installed on the server" (サーバーにインストールされているアプリケーションを検出できません) | この問題は、内部エラーのために発生します。  | 24 時間以内に問題が自動的に解決しない場合は、サポートにお問い合わせください。
9013:"Unable to discover the applications installed on the server" (サーバーにインストールされているアプリケーションを検出できません) | VM にログインするたびに、新しい一時プロファイルが作成されます。  | 指定されたゲスト ユーザーに対して一時プロファイルが作成されていないことを確認してください。



## <a name="next-steps"></a>次のステップ
[VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md)、または[物理サーバー](how-to-set-up-appliance-physical.md)用にアプライアンスを設定します。

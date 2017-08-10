---
title: "Azure RemoteApp の課金の変更 | Microsoft Docs"
description: "Azure RemoteApp の課金を停止する方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>Azure RemoteApp から MyCloudIT に移行する 

**現在 Microsoft Azure RemoteApp を使用している場合**: MyCloudIT は、Microsoft Azure の実行を続行しながら Azure RemoteApp (ARA) コレクションを MyCloudIT 管理プラットフォームに移行するための自動化ツールを構築します。

**Azure Resource Manager ポータルを活用**: MyCloudIT プラットフォームへの移行が完了すると、Azure の新しい Azure Resource Manager ポータルに簡単にアクセスできます。 このポータルには、確実に業務要件に対応する仮想マシンのサイズを利用可能なデプロイにするなど、Microsoft Azure で提供されているすべての新機能と技術革新が含まれています。

**テストの並列実行によりニーズに最適なソリューションを実現**: MyCloudIT 移行ツールでは、現在の ARA ユーザーが ARA の使用を続けながら、移行プロセスを開始し、テストを並列に実行できます。  ユーザーが ARA を使用している間に移行およびテストを完了することができます。  移行ツールは、一般的な ARA コレクションを処理するように構築されています。  特殊な、非標準的なシナリオと思われる場合は、[sales@conexlink.com](mailto:sales@conexlink.com) までお問い合わせください。移行を支援するカスタマイズされたプランをご用意することができます。

**Desktop-as-a-Service 機能**: MyCloudIT は、おなじみの RemoteApp 機能のほかに、同じ月間料金で最小ユーザー要件なしに完全な Desktop-as-a-Service 機能を提供しています。

## <a name="what-we-will-do-for-you"></a>MyCloudIT の自動化機能

MyCloudIT では、自動移行ツールを使用して、自分のサブスクリプションの Azure クラシック ポータルから Azure Resource Manager ポータルへの Azure RemoteApp テンプレートの移行を自動化できます。  

> [!NOTE]
> Azure RemoteApp テンプレートは、元の Azure RemoteApp のデプロイと同じ Azure リージョンに存在する必要があります。  移行中に Azure リージョンまたは Azure サブスクリプションを変更する場合の追加のガイダンスについては、[sales@conexlink.com](mailto:sales@conexlink.com) までお問い合わせください。

MyCloudIT 移行ツールを使用した移行プロセスの自動化の詳細については、以下をお読みください。

1. 移行ツールは、すべての既存の ARA デプロイを対象にして現在のサブスクリプションをスキャンします。  
2. 一度に 1 つの ARA コレクションを選択します。  複数のコレクションがある場合は、移行ツールを複数回実行します。
3. 新しいデプロイにユーザー プロファイル ディスク (UPD) をコピーするオプションがあるので、従来のデータを取得するか、新しいデプロイに UPD を手動でマップするかを選択できます。 UPD をコピーすることを選択すると、UPD は保存され、UPD 名を各ユーザーの名前にマップするテキスト ファイルが追加されます。  UPD は RDSMGMT サーバー上の共有 `F:\Shares\LegacyUPD` にコピーされ、共有 `\\RDSmgmt\LegacyUPD` によって公開されます。 
4. 移行には、現在の ARA デプロイのダウンタイムは要しません。  ただし、コピーした後に (ARA から) UPD を変更しても、Azure Resource Manager ポータルに格納された UPD には反映されません。 
5. クラシック Azure Virtual Network にドメイン コントローラーやファイル サーバーなどの追加 VM がある場合、新しい Azure Resource Manager ポータルで、既存のクラシック Azure Virtual Network と自動作成された新しい Virtual Network との VNet ピアリングが確立されます。
6. 自動化ソリューションでは、既存の ARA デプロイがハイブリッド デプロイの場合、つまり、既存のクラシック Azure Virtual Network の Windows Server Active Directory ドメイン コントローラーで認証している場合にのみ、既存のクラシック Azure Virtual Network と新しい Virtual Network との VNet ピアリングが確立されます。 コレクションの VNet ピアリングが確立されない場合、VNet ピアリングが必要であれば、[sales@conexlink.com](mailto:sales@conexlink.com) までご連絡ください。追加料金なしで VNet ピアリングを構成します。
7. 自動化ソリューションにより、常に新しい Virtual Network 設定で Azure DNS の構成が更新され、新しいデプロイで従来の VNet の既存のドメイン コントローラーに確実に接続できます。
8. 自動化ソリューションでは、この新しい Virtual Network を作成し、既存の Windows Server Active Directory サーバーが存在するデプロイに対して VNet ピアリングを確立するため、IP アドレスの競合が生じません。
9. 認証に Azure AD のみを使用している場合、MyCloudIT は新しい Windows Server Active Directory ドメインを作成し、Azure AD Connect を使用して既存の Azure AD インスタンスと新しい Windows Server Active Directory ドメインとの間でユーザーを同期します。
10. ARA ユーザーの認証に Windows Server Active Directory ドメインを使用している場合、新しい MyCloudIT のデプロイは VNet ピアリング経由で既存の Windows Server Active Directory ドメイン コントローラーに接続されます。
11. 認証に Azure Active Directory Domain Services を使用している場合、移行することは可能ですが、お問い合わせいただけば、カスタム移行プランをご用意できます。  [sales@conexlink.com](mailto:sales@conexlink.com) まで電子メールでご連絡ください。 
12. 移行するコレクションを確認すれば、ARA コレクションとユーザー プロファイル ディスク (オプション) を新しい MyCloudIT に基づくリモート アプリ ソリューションに移行する処理を自動化ソリューションが自動的に行います。
13. デプロイが完了したら、ARA で発行されたアプリケーションと同じアプリケーションを再発行します。デプロイ後に、ユーザーは追加アプリケーションを発行できます。

## <a name="post-migration-benefits"></a>移行後のメリット

1. リモート アプリ デプロイのライフ サイクル全体を管理できる管理コンソールを利用できます。
2. ポータルから Virtual Machines を管理できます。  必要に応じて VM を個別に開始、停止、サイズ変更できます。
3. 管理コンソールでは、ユーザーまたはグループを管理ポータルから作成および管理できます。
4. 管理コンソールを使用して、Office 365 でユーザーを同期し、同じサインオン エクスペリエンスを作成できます。
5. 管理コンソールを使用して、ユーザー コストの重複や最小ユーザー要件なしに、リモート アプリとデスクトップ コレクションを追加作成できます。 
6. 管理コンソールを使用して、新しいリモート アプリのアプリケーションを公開できます。
7. 管理コンソールを使用して、特定の時間中にのみソリューションが必要な場合に、リモート アプリのデプロイの起動とシャットダウンをスケジュールできます。
8. 管理コンソールを使用して、顧客データのドキュメント リテンション履歴を提供する Azure Backup エージェントのインストールと構成を自動化できます。
9. 管理コンソールを使用して、デプロイのパフォーマンス メトリックにアクセスできます。  これによって、追加のパフォーマンス管理ツールをインストールしなくても、潜在的なパフォーマンスのボトルネックを特定できます。
10. 複数のセッション ホストが存在する場合、自動サイズ調整により、必要なセッション ホストのみが実行されるようにすることができます。
11. MyCloudIT では、MyCloudIT ドメイン名を使用して RDWeb ゲートウェイ サーバーにアクセスできます。  エンド ユーザー ブランディング用に URL をカスタム URL に再マップできます。

## <a name="prerequisites-for-migration"></a>移行の前提条件

1. 現在の Azure RemoteApp ソリューションをホストする Azure サブスクリプションへのアクセスが必要です。
2. テンプレートを移行し、新しい MyCloudIT のデプロイを作成/変更するアクセス許可をサブスクリプション内の当ポータルに付与する必要があります。
3. Azure リモート アプリの制限により、各コレクションを移行できるのは 3 回までです。  コレクションを 3 回以上移行する必要がある場合、 Azure に対してエクスポート カウントを増やすチケットを生成するか、またはお問い合わせください。エクスポート カウントを増やす ARA 要求をサポートします。

## <a name="mycloudit-billing"></a>MyCloudIT の課金

Azure の全体的なコストを予測および管理する方法については、「[MyCloudIT Pricing for RemoteApp Solutions](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf)」(MyCloudIT の RemoteApp ソリューション料金) を参照してください。

その他にご質問がある場合は、[sales@conexlink.com](mailto:sales@conexlink.com) までお問い合わせください。または、[(Azure RemoteApp 移行ツール - MyCloudIT)](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s) の完全なデモ動画をご覧ください。 



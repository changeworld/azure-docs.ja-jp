---
title: "Azure Linux 仮想マシンのドキュメント - チュートリアル、API リファレンス | Microsoft Docs"
description: "選択した Linux ディストリビューションを使用して仮想マシンを作成する方法について説明します。 ドキュメントでは、VM テンプレートを作成するさまざまな方法を紹介します。"
services: virtual-machines\linux
author: carolz
manager: carolz
layout: LandingPage
ms.service: virtual-machines\linux
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: e4fede90a5df7c4a74c1e1d41ecbe669d731a1b5
ms.lasthandoff: 04/23/2017

---
<div class="content">
    <h1>Linux 仮想マシン</h1>
    <div class="introHolder" style="justify-content: space-between;">
        <div class="intro" style="min-width: 200px">
            <p>Azure Linux 仮想マシンでは、Red Hat、Ubuntu、または好みの Linux ディストリビューションを使用して、セキュリティで保護された高スケールな仮想化インフラストラクチャをオンデマンドで構築できます。 Microsoft が提供するクイック スタート、チュートリアル、サンプルを使用して、Linux VM の作成、構成、管理、スケーリングを行う方法を確認してください。</p>
        </div>
        <a href="https://azure.microsoft.com/en-us/resources/videos/create-linux-virtual-machine/">
            <div class="calloutHolder" style="max-width: 250px">
                <div>
                    <img src="media/index/create-linux-virtual-machine.png" style="width: 250px" />
                </div>
                <div>
                    <p style="margin-top: 0; color: #6e6e6e">Linux 仮想マシンを作成する。 (4:11)</p>
                </div>
            </div>
        </a>
    </div>
<h2 style="margin-top: 0px; margin-bottom: 0px;">5 分間のクイック スタート</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">Ubuntu を実行している仮想マシン内で NGINX Web サーバーをデプロイする方法について説明します。</p>
<div class="ico48Case">
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/cli.svg" alt="">
            <span>Azure CLI</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-portal?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
        <img src="media/index/portal.svg" alt="">
            <span>Azure Portal</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-powershell?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/logo_powershell.svg" alt="">
            <span>Azure PowerShell</span>
        </a>
    </div>
<div>

<h2 style="margin-top: 36px">ステップバイステップのチュートリアル</h2>
<p>Azure で Linux VM のデプロイ、管理、スケーリング行う方法について説明します</p>
<ol>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-vm">Linux VM の作成と管理</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-disks">VM ディスクの作成と管理</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-automate-vm-deployment">VM 構成の自動化</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-custom-images">カスタム VM イメージを作成する</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-availability-sets">高可用性 VM の作成</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-create-vmss">VM スケール セットの作成</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-load-balancer">VM の負荷分散</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-virtual-network">VM ネットワークの管理</a></li>
</ol>

<h2 style="margin-top: 36px">無料の Pluralsight ビデオ トレーニング</h2>
<ul class="panelContent cardsW">
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-infrastructure.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>インフラストラクチャの管理</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-vms.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>Virtual Machines の概要</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-iaas-monitoring.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>IaaS 監視の概要</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<h2>サンプル</h2>
<p>初めてのアプリケーションを Azure にデプロイします。</p>
<ul class="spaced">
    <li><a href="/azure/virtual-machines/virtual-machines-linux-cli-samples">Azure CLI</a></li>
    <li><a href="/azure/virtual-machines/virtual-machines-linux-powershell-samples">Azure PowerShell</a></li>
</ul>

<h2 style="margin-top: 36px">リファレンス</h2>
<ul class="panelContent cardsW">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>コマンド ライン</h3>
                        <p><a href="/cli/azure/vm">Azure CLI</a></p>
                        <p><a href="/powershell/azureps-cmdlets-docs">Azure PowerShell</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>言語</h3>
                        <p><a href="/dotnet/api/microsoft.azure.management.compute">.NET</a></p>
                        <p><a href="/java/api">Java</a></p>
                        <p><a href="https://azure.microsoft.com/en-us/develop/nodejs/#azure-sdk">Node.JS</a></p>
                        <p><a href="http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.compute.html">Python</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>REST ()</h3>
                        <p><a href="/rest/api/compute">REST API リファレンスに関するページ</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>
</div>


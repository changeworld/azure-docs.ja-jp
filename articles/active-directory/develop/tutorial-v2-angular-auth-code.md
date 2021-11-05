---
title: 'チュートリアル: 認証コード フローを使って、認証に Microsoft ID プラットフォームを使用する Angular アプリを作成する | Azure'
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、認証コード フローを使い、Microsoft ID プラットフォームを使用してユーザーのサインインを処理し、アクセス トークンを取得してそのユーザーに代わって Microsoft Graph API を呼び出す Angular シングルページ アプリ (SPA) を作成します。
services: active-directory
author: jo-arroyo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/14/2021
ms.author: joarroyo
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 398117120e560db00be97a344feed09351a26258
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050339"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa-using-auth-code-flow"></a>チュートリアル: 認証コード フローを使用して Angular シングルページ アプリケーション (SPA) からユーザーをサインインさせて Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを行い、PKCE による承認コード フローを使用して Microsoft Graph API を呼び出す Angular シングルページ アプリケーション (SPA) を構築します。 構築する SPA では、Angular v2 用の Microsoft Authentication Library (MSAL) を使用します。

このチュートリアルの内容:

> [!div class="checklist"]
> * `npm` で Angular プロジェクトを作成する
> * Azure portal でアプリケーションを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

MSAL Angular v2 は、暗黙的な許可のフローではなくブラウザーでの承認コード フローをサポートすることで、MSAL Angular v1 よりも強化されています。 MSAL Angular v2 では、暗黙的フローはサポートされて **いません**。

## <a name="prerequisites"></a>前提条件

* ローカル Web サーバーを実行するための [Node.js](https://nodejs.org/en/download/)。
* プロジェクト ファイルを編集するためのエディター ([Visual Studio Code](https://code.visualstudio.com/download) など)。

## <a name="how-the-sample-app-works"></a>このサンプル アプリのしくみ

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="シングルページ アプリケーションでの認証コード フローを示す図":::

このチュートリアルで作成したサンプル アプリケーションを使用すると、Angular SPA で、Microsoft ID プラットフォームによって発行されたトークンを受け取る Microsoft Graph API や Web API に対してクエリを実行することができます。 Angular v2 用の Microsoft Authentication Library (MSAL)、MSAL.js v2 ライブラリのラッパーを使用します。 MSAL Angular では、Angular (9 以降) のアプリケーションで Azure Active Directory (Azure AD) を使用してエンタープライズ ユーザーを認証できるほか、Microsoft アカウントを持つユーザーとソーシャル ID (Facebook、Google、LinkedIn など) を認証することができます。 また、アプリケーションは、このライブラリを通じて、Microsoft クラウド サービスや Microsoft Graph にアクセスすることができます。

このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、MSAL によって処理されます。

### <a name="libraries"></a>ライブラリ

このチュートリアルでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)|JavaScript Angular Wrapper 用の Microsoft Authentication Library|
|[MSAL ブラウザー](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|JavaScript v2 ブラウザー パッケージ用の Microsoft Authentication Library |

すべての MSAL.js ライブラリのソース コードは、GitHub の [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) リポジトリにあります。

## <a name="create-your-project"></a>プロジェクトを作成する

[Node.js](https://nodejs.org/en/download/) をインストールしたら、ターミナル ウィンドウを開いて次のコマンドを実行し、新しい Angular アプリケーションを生成します。

```bash
npm install -g @angular/cli                         # Install the Angular CLI
ng new msal-angular-tutorial --routing=true --style=css --strict=false    # Generate a new Angular app
cd msal-angular-tutorial                            # Change to the app directory
npm install @angular/material @angular/cdk          # Install the Angular Material component library (optional, for UI)
npm install @azure/msal-browser @azure/msal-angular # Install MSAL Browser and MSAL Angular in your application
ng generate component home                          # To add a home page
ng generate component profile                       # To add a profile page
```

## <a name="register-your-application"></a>アプリケーションの登録

Azure portal に[シングルページ アプリケーションを登録する手順](./scenario-spa-app-registration.md)を実行します。

後で使用するために、登録の **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。

**リダイレクト URI** の値を **http://localhost:4200/** 、種類を "SPA" として登録します。

## <a name="configure-the-application"></a>アプリケーションを構成する

1. *src/app* フォルダーで、*app.module.ts* を編集して、`MsalModule` および `MsalInterceptor` を `imports` に追加し、`isIE` 定数を追加します。 コードは次のようになります。

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

    次の値を置き換えます。

    |値の名前|概要|
    |---------|---------|
    |Enter_the_Application_Id_Here|これは、アプリケーションの登録の **[概要]** ページの **[アプリケーション (クライアント) ID]** の値です。 |
    |Enter_the_Cloud_Instance_Id_Here|これは、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、「 **https://login.microsoftonline.com** 」と入力します。 各国のクラウド (中国など) の場合は、「[各国のクラウド](./authentication-national-cloud.md)」を参照してください。|
    |Enter_the_Tenant_Info_Here| 次のいずれかのオプションに設定します。1) お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値をディレクトリ (テナント) ID またはテナント名 (例: "**contoso.microsoft.com**") に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。 アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。 |
    |Enter_the_Redirect_Uri_Here|**http://localhost:4200** に置き換えます。|

    使用できる構成オプションの詳細については、[クライアント アプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

2. *src/app/app-routing.module.ts* で home および profile のコンポーネントにルートを追加します。 コードは次のようになります。

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

## <a name="replace-base-ui"></a>ベース UI を置き換える

1. *src/app/app.component.html* のプレースホルダー コードを次のように置き換えます。

    ```HTML
    <mat-toolbar color="primary">
      <a class="title" href="/">{{ title }}</a>

      <div class="toolbar-spacer"></div>

      <a mat-button [routerLink]="['profile']">Profile</a>

      <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>

    </mat-toolbar>
    <div class="container">
      <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
      <router-outlet *ngIf="!isIframe"></router-outlet>
    </div>
    ```

2. material モジュールを *src/app/app.module.ts* に追加します。 `AppModule` は次のようになります。

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, 
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

3. (省略可能) CSS を *src/style.css* に追加します。

    ```css
    @import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

    html, body { height: 100%; }
    body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
    .container { margin: 1%; }
    ```

4. (省略可能) CSS を *src/app/app.component.css* に追加します。

    ```css
    .toolbar-spacer {
        flex: 1 1 auto;
      }
      
      a.title {
        color: white;
      }
    ```

## <a name="sign-in-a-user"></a>ユーザーのサインイン

次のセクションからコードを追加し、ポップアップ ウィンドウまたはフルフレーム リダイレクトを使用したログインを呼び出します。 

### <a name="sign-in-using-pop-ups"></a>ポップアップを使用したサインイン

1. ポップアップ ウィンドウを使用してユーザーをサインインさせるよう、*src/app/app.component.ts* のコードを次のように変更します。

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginPopup()
          .subscribe({
            next: (result) => {
              console.log(result);
              this.setLoginDisplay();
            },
            error: (error) => console.log(error)
          });
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

> [!NOTE]
> このチュートリアルの残りの部分では、`loginRedirect` メソッドと共に Microsoft Internet Explorer を使用しています。これは、Internet Explorer によるポップアップ ウィンドウの処理に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md)があるためです。

### <a name="sign-in-using-redirects"></a>リダイレクトを使用したサインイン

1. `MsalRedirectComponent` をブートストラップするように *src/app/app.module.ts* を更新します。 これは、リダイレクトを処理する専用の redirect コンポーネントです。 コードは次のようになっているはずです。

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular'; // Updated import
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent, MsalRedirectComponent] // MsalRedirectComponent bootstrapped here
    })
    export class AppModule { }
    ```

2. `<app-redirect>` セレクターを *src/index.html* に追加します。 このセレクターは `MsalRedirectComponent` によって使用されます。 *src/index.html* は次のようになります。

    ```HTML
    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>msal-angular-tutorial</title>
      <base href="/">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <link rel="icon" type="image/x-icon" href="favicon.ico">
    </head>
    <body>
      <app-root></app-root>
      <app-redirect></app-redirect>
    </body>
    </html>
    ```

3. フルフレーム リダイレクトを使用してユーザーをサインインさせるよう、*src/app/app.component.ts* のコードを次のように置き換えます。

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

4. `LOGIN_SUCCESS` イベントをサブスクライブするように *src/app/home/home.component.ts* の既存のコードを置き換えます。 これにより、リダイレクトによって成功したログインの結果にアクセスできるようになります。 コードは次のようになります。

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
    import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
    import { filter } from 'rxjs/operators';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {
      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
      }
    }
    ```

## <a name="conditional-rendering"></a>条件付きの表示

認証済みのユーザーにのみ特定の UI を表示するには、ユーザーがサインイン済みでやり取りが完了していることをコンポーネントが認識できるように、`MsalBroadcastService` をサブスクライブする必要があります。

1. やり取りが完了していてアカウントがサインインしているかどうかを UI の表示前にチェックするように、`MsalBroadcastService` を *src/app/app.component.ts* に追加して監視可能な `inProgress$` をサブスクライブします。 コードは次のようになっているはずです。

    ```javascript
    import { Component, OnInit, OnDestroy } from '@angular/core';
    import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
    import { InteractionStatus } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

        this.broadcastService.inProgress$
        .pipe(
          filter((status: InteractionStatus) => status === InteractionStatus.None),
          takeUntil(this._destroying$)
        )
        .subscribe(() => {
          this.setLoginDisplay();
        })
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

2. やり取りが完了しているかどうかを UI の更新前にもチェックするように、*src/app/home/home.component.ts* のコードを更新します。 コードは次のようになっているはずです。

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
    import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
    import { filter } from 'rxjs/operators';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {
      loginDisplay = false;

      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
        
        this.msalBroadcastService.inProgress$
          .pipe(
            filter((status: InteractionStatus) => status === InteractionStatus.None)
          )
          .subscribe(() => {
            this.setLoginDisplay();
          })
      }
      
      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

3. *src/app/home/home.component.html* のコードを次の条件付きの表示に置き換えます。

    ```HTML
    <div *ngIf="!loginDisplay">
        <p>Please sign-in to see your profile information.</p>
    </div>

    <div *ngIf="loginDisplay">
        <p>Login successful!</p>
        <p>Request your profile information by clicking Profile above.</p>
    </div>
    ```

## <a name="guarding-routes"></a>ルートの保護

### <a name="angular-guard"></a>Angular Guard

MSAL Angular には、ルートを保護し、保護されたルートにアクセスする前に認証を要求するために使用できるクラス `MsalGuard` があります。 以下の手順では `MsalGuard` を `Profile` ルートに追加します。 `Profile` ルートを保護すると、ユーザーは `Login` ボタンを使用してサインインしていなくても、`Profile` ルートにアクセスまたは `Profile` ボタンをクリックしようとした際に、`Profile` ページの表示前にポップアップまたはリダイレクトを介した認証を行うように `MsalGuard` から求められます。

`MsalGuard` はユーザー エクスペリエンスを改善できる便利なクラスですが、セキュリティという点では信頼すべきでありません。 攻撃者がクライアント側の保護を回避する可能性があるので、ユーザーによってアクセスされるべきでないデータをサーバーが返すことのないようにする必要があります。

1. `MsalGuard` クラスをアプリケーションのプロバイダーとして *src/app/app.module.ts* に追加し、`MsalGuard` の構成を追加します。 後からトークンを取得するために必要なスコープは、`authRequest` で指定できます。Guard のやり取りの種類は `Redirect` または `Popup` に設定できます。 コードは次のようになります。

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard } from '@azure/msal-angular'; // MsalGuard added to imports
    import { PublicClientApplication, InteractionType } from '@azure/msal-browser'; // InteractionType added to imports

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
            interactionType: InteractionType.Redirect, // MSAL Guard Configuration
            authRequest: {
              scopes: ['user.read']
            }
        }, null)
      ],
      providers: [
        MsalGuard // MsalGuard added as provider here
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }
    ```

2. *src/app/app-routing.module.ts* で保護したいルートで `MsalGuard` を設定します。

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';
    import { MsalGuard } from '@azure/msal-angular';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [MsalGuard]
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

3. 保護構成で設定された `authRequest` が考慮されるように、*src/app/app.component.ts* でログイン呼び出しを調整します。 これでコードは次のようになります。

    ```javascript
    import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
    import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
    import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

        this.broadcastService.inProgress$
        .pipe(
          filter((status: InteractionStatus) => status === InteractionStatus.None),
          takeUntil(this._destroying$)
        )
        .subscribe(() => {
          this.setLoginDisplay();
        })
      }

      login() {
        if (this.msalGuardConfig.authRequest){
          this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
        } else {
          this.authService.loginRedirect();
        }
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

## <a name="acquire-a-token"></a>トークンを取得する

### <a name="angular-interceptor"></a>Angular Interceptor

MSAL Angular には `Interceptor` クラスが用意されています。これは、既知の保護されたリソースに対して Angular の `http` クライアントを使用する送信要求のためにトークンを自動的に取得します。

1. *src/app/app.module.ts* で `Interceptor` クラスをプロバイダーとして (その構成と一緒に) アプリケーションに追加します。 コードは次のようになっているはずです。

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';
    import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http"; // Import 

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard, MsalInterceptor } from '@azure/msal-angular'; // Import MsalInterceptor
    import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        HttpClientModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_Here',
            authority: 'Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here',
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
          interactionType: InteractionType.Redirect,
          authRequest: {
            scopes: ['user.read']
            }
        }, {
          interactionType: InteractionType.Redirect, // MSAL Interceptor Configuration
          protectedResourceMap: new Map([ 
              ['Enter_the_Graph_Endpoint_Here/v1.0/me', ['user.read']]
          ])
        })
      ],
      providers: [
        {
          provide: HTTP_INTERCEPTORS,
          useClass: MsalInterceptor,
          multi: true
        },
        MsalGuard
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }

    ```

    保護されるリソースは `protectedResourceMap` として指定されます。 `protectedResourceMap` コレクションに指定する URL は、大文字と小文字が区別されます。 リソースごとに、アクセス トークンで返されるように要求されているスコープを追加します。

    次に例を示します。

    * Microsoft Graph の場合 `["user.read"]`
    * カスタム Web API の場合 `["<Application ID URL>/scope"]` (つまり、`api://<Application ID>/access_as_user`)
    
    次の説明のとおりに `protectedResourceMap` の値を変更します。

    |値の名前| 概要|
    |----------|------|
    |`Enter_the_Graph_Endpoint_Here`| アプリケーションが通信する必要がある、Microsoft Graph API のインスタンス。 **グローバル** Microsoft Graph API エンドポイントの場合は、この文字列の両方のインスタンスを `https://graph.microsoft.com` に置き換えます。 **各国** のクラウドのデプロイにおけるエンドポイントの場合は、Microsoft Graph のドキュメントで「[各国のクラウドでのデプロイ](/graph/deployments)」を参照してください。|

2. HTTP 要求でユーザーのプロファイルを取得できるように、*src/app/profile/profile.component.ts* のコードを置き換えます。

    ```JavaScript
    import { Component, OnInit } from '@angular/core';
    import { HttpClient } from '@angular/common/http';

    const GRAPH_ENDPOINT = 'Enter_the_Graph_Endpoint_Here/v1.0/me';

    type ProfileType = {
      givenName?: string,
      surname?: string,
      userPrincipalName?: string,
      id?: string
    };

    @Component({
      selector: 'app-profile',
      templateUrl: './profile.component.html',
      styleUrls: ['./profile.component.css']
    })
    export class ProfileComponent implements OnInit {
      profile!: ProfileType;

      constructor(
        private http: HttpClient
      ) { }

      ngOnInit() {
        this.getProfile();
      }

      getProfile() {
        this.http.get(GRAPH_ENDPOINT)
          .subscribe(profile => {
            this.profile = profile;
          });
      }
    }
    ```

3. プロファイル情報を表示するように、*src/app/profile/profile.component.html* で UI を置き換えます。 

    ```HTML
    <div>
        <p><strong>First Name: </strong> {{profile?.givenName}}</p>
        <p><strong>Last Name: </strong> {{profile?.surname}}</p>
        <p><strong>Email: </strong> {{profile?.userPrincipalName}}</p>
        <p><strong>Id: </strong> {{profile?.id}}</p>
    </div>
    ```

## <a name="sign-out"></a>サインアウト

条件に従って `Logout` ボタンを表示するように、*src/app/app.component.html* のコードを更新します。

```HTML
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
  <router-outlet *ngIf="!isIframe"></router-outlet>
</div>
```

### <a name="sign-out-using-redirects"></a>リダイレクトを使用したサインアウト

リダイレクトを使用してユーザーをサインアウトさせるように、*src/app/app.component.ts* のコードを更新します。

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { // Add log out function here
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

### <a name="sign-out-using-pop-ups"></a>ポップアップを使用したサインアウト

ポップアウトを使用してユーザーをサインアウトさせるように、*src/app/app.component.ts* のコードを更新します。

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, PopupRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest)
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    } else {
      this.authService.loginPopup()
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    }
  }

  logout() { // Add log out function here
    this.authService.logoutPopup({
      mainWindowRedirectUri: "/"
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

## <a name="test-your-code"></a>コードのテスト

1.  アプリケーション フォルダーからコマンドライン プロンプトで次のコマンドを実行して、Web サーバーを起動してポートをリッスンします。

    ```bash
    npm install
    npm start
    ```
1. ブラウザーに「 **http://localhost:4200** 」または「 **http://localhost:{port}** 」と入力します。ここで、*port* は、Web サーバーがリッスンしているポートです。 次のようなページが表示されます。

    :::image type="content" source="media/tutorial-v2-angular-auth-code/angular-01-not-signed-in.png" alt-text="サインイン ダイアログを表示している Web ブラウザー":::


### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスをアプリケーションに許可してサインインに同意するよう求められます。

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Web ブラウザーに表示されているコンテンツ ダイアログ":::

要求されたアクセス許可に同意すると、Web アプリケーションによってログイン ページが正常に表示されます。

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-02-signed-in.png" alt-text="Web ブラウザーでの成功したサインインの結果":::

### <a name="call-the-graph-api"></a>Graph API を呼び出す

サインインした後、 **[Profile]** を選択して、Microsoft Graph API への呼び出しからの応答で返されるユーザー プロファイル情報を表示します。

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-03-profile-data.png" alt-text="ブラウザーに表示される Microsoft Graph からのプロファイル情報":::

## <a name="add-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可を追加する

Microsoft Graph API には、ユーザーのプロファイルを読み取るための _User.Read_ スコープが必要です。 _User.Read_ スコープは、Azure portal で作成するすべてのアプリ登録に自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API では、ユーザーのメールを一覧表示するために _Mail.Read_ スコープが必要です。

スコープを追加すると、追加したスコープに対して追加の同意を求めるメッセージがユーザーに表示される場合があります。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームにおけるシングルページ アプリケーション (SPA) 開発の詳細を、複数のパートから成る一連の記事でご覧ください。

> [!div class="nextstepaction"]
> [シナリオ:シングルページ アプリ](scenario-spa-overview.md)

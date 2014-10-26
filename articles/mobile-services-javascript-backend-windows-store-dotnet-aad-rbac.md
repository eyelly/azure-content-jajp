<properties linkid="develop-mobile-tutorials-js-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc"></tags>

# Mobile Services と Azure Active Directory でのロール ベースのアクセス制御

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Windows ストア C#" class="current">Windows ストア C#</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title=".NET バックエンド">.NET バックエンド</a> |
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

ロール ベースのアクセス制御 (RBAC) は、ユーザーが保持できるロールにアクセス許可を割り当てる機能であり、特定のクラスのユーザーが実行できることとできないことの境界線を明確に定義します。このチュートリアルでは、Azure Mobile Services に基本的な RBAC を追加する方法について説明します。

このチュートリアルでは、ロール ベースのアクセス制御について示しながら、Azure Active Directory (AAD) で定義された Sales グループに対する各ユーザーのメンバーシップを確認します。アクセスの確認は、モバイル サービス バックエンドで Azure Active Directory 用の [Graph API][Graph API] を使用し、JavaScript で行います。Sales ロールに属するユーザーのみが、データの照会を許可されます。

> [WACOM.NOTE] このチュートリアルは、認証方法を含めた認証の知識を深めることを目的としています。最初に、Azure Active Directory 認証プロバイダーを使用して、「[認証の使用][認証の使用]」チュートリアルを完了しておく必要があります。このチュートリアルでは、「[認証の使用][認証の使用]」チュートリアルで使用した TodoItem アプリケーションを引き続き更新します。

このチュートリアルでは、次の手順について説明します。

1.  [Sales グループとメンバーシップを作成する][Sales グループとメンバーシップを作成する]
2.  [統合アプリケーションのキーを生成する][統合アプリケーションのキーを生成する]
3.  [メンバーシップを確認する共有スクリプトを追加する][メンバーシップを確認する共有スクリプトを追加する]
4.  [データベース操作にロール ベースのアクセス確認を追加する][データベース操作にロール ベースのアクセス確認を追加する]
5.  [クライアントのアクセスをテストする][クライアントのアクセスをテストする]

このチュートリアルには、次のものが必要です。

-   Windows 8.1 で実行されている Visual Studio 2013。
-   Azure Active Directory 認証プロバイダーを使用して、「[認証の使用][認証の使用]」チュートリアルを完了していること。
-   「[Store Server Scripts (サーバー スクリプトの格納)][Store Server Scripts (サーバー スクリプトの格納)]」チュートリアルを完了し、Git リポジトリを使用してサーバー スクリプトを格納する方法に習熟していること。

## <a name="create-group"></a>Sales グループとメンバーシップを作成する

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group][mobile-services-aad-rbac-create-sales-group]]

## <a name="generate-key"></a>統合アプリケーションのキーを生成する

「[認証の使用][認証の使用]」チュートリアルでは、手順「[Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)][Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]」を完了したときに、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="add-shared-script"></a>メンバーシップを確認する共有スクリプトをモバイル サービスに追加する

このセクションでは、Git を使用して *rbac.js* という名前の共有スクリプト ファイルをモバイル サービスにデプロイします。この共有スクリプト ファイルには、[Graph API][Graph API] を使用してユーザーのグループ メンバーシップを確認する関数が含まれています。

Git でモバイル サービスにスクリプトをデプロイすることに慣れていない場合は、このセクションを完了する前に、「[Store Server Scripts (サーバー スクリプトの格納)][Store Server Scripts (サーバー スクリプトの格納)]」チュートリアルを見直してください。

1.  モバイル サービスのローカル リポジトリの *./service/shared/* ディレクトリに *rbac.js* という名前の新しいスクリプト ファイルを作成します。
2.  `getAADToken` 関数を定義するファイルの先頭に次のスクリプトを追加します。*tenant\_domain*、統合アプリケーションの *client id*、およびアプリケーションの *key* を与えることで、この関数はディレクトリ情報の読み取りに使用する Graph アクセス トークンを提供します。

    > [WACOM.NOTE] アクセス確認のたびに新しいトークンを作成するのではなく、トークンをキャッシュする必要があります。その後、「[Windows Azure AD Graph のエラー コード][Windows Azure AD Graph のエラー コード]」に記載されているとおり、トークンの使用を試みたときに 401 Authentication\_ExpiredToken 応答が返された場合は、キャッシュを更新します。これは、次に示すコードでは簡略化のために省かれていますが、Active Directory に対して余分なネットワーク トラフィックを軽減する効果があります。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
                });
        }

3.  次のコードを *rbac.js* に追加して `getGroupId` 関数を定義します。この関数は、アクセス トークンを使用して、フィルターで使用されたグループ名に基づいてグループ ID を取得します。

    > [WACOM.NOTE] このコードは、Active Directory グループを名前で検索します。多くの場合、モバイル サービスのアプリケーション設定としてグループ ID を格納し、そのグループ ID を使用するのがよい方法と言えます。グループ名は変更になる可能性がありますが、ID は変わらないからです。ただし、グループ名が変更されると、通常、少なくともロールのスコープに変更が生じるため、モバイル サービスのコードも更新が必要になる場合があります。

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
                  "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }

4.  次のコードを *rbac.js* に追加して、Graph REST API の[IsMemberOf][IsMemberOf] エンドポイントを呼び出す `isMemberOf` 関数を定義します。

    この関数は、Graph REST API の[IsMemberOf][IsMemberOf] エンドポイントの thin ラッパーです。Graph アクセス トークンを使用して、ユーザーのディレクトリ オブジェクト ID が、グループ ID に基づくグループのメンバーシップを持っているかどうか確認します。

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

5.  エクスポートされた次の `checkGroupMembership` 関数を *rbac.js* に追加します。

    この関数は他のスクリプト関数の使用をラップし、他のスクリプトによって呼び出される共有スクリプトからエクスポートされ、実際にアクセス確認を実行します。モバイル サービスのユーザー オブジェクトおよびグループ ID が与えられると、このスクリプトはユーザーの ID に対する Azure Active Directory オブジェクト ID を取得して、そのグループのメンバーシップを確認します。

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
                else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
                                if (err) errorHandler(err);
                                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

6.  変更を *rbac.js* に保存します。

## <a name="add-access-checking"></a>データベース操作にロール ベースのアクセス確認を追加する

「[認証の使用][認証の使用]」チュートリアルを完了すると、次に示すように、認証が必要になるテーブル操作を既に設定したことになります。

![][]

認証が必要なデータベース操作それぞれに対して、ユーザー オブジェクトを使用してアクセスを確認するスクリプトを追加できます。

次の手順には、このスクリプトを使用してロール ベースのアクセス制御をモバイル サービスの各テーブル操作にデプロイする方法を示します。共有 *rbac.js* スクリプトを使用するスクリプトが、各テーブル操作に対して実行されます。

1.  *todoitem.insert.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function insert(item, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2.  *todoitem.read.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function read(query, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3.  *todoitem.update.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function update(item, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4.  *todoitem.delete.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function del(id, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5.  Git リポジトリへのコマンド ライン インターフェイスで次のコマンドを実行することにより、追加したスクリプト ファイルの追跡を追加します。

        git add .

6.  Git リポジトリへのコマンド ライン インターフェイスで次のコマンドを実行して、更新をコミットします。

        git commit -m "Added role based access control to table operations"

7.  Git リポジトリへのコマンド ライン インターフェイスで次のコマンドを実行して、ローカル Git リポジトリへの更新をモバイル サービスにデプロイします。このコマンドは、*master* 分岐で更新を行ったと想定しています。

        git push origin master

8.  [Azure の管理ポータル][Azure の管理ポータル]では、モバイル サービスに対するテーブル操作に移動して、以下に示すような更新が行われたことを確認できます。

    ![][1]

## <a name="test-client"></a>クライアントのアクセスをテストする

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app][mobile-services-aad-rbac-test-app]]



  [Windows ストア C\#]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "Windows ストア C#"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "JavaScript バックエンド"
  [Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Sales グループとメンバーシップを作成する]: #create-group
  [統合アプリケーションのキーを生成する]: #generate-key
  [メンバーシップを確認する共有スクリプトを追加する]: #add-shared-script
  [データベース操作にロール ベースのアクセス確認を追加する]: #add-access-checking
  [クライアントのアクセスをテストする]: #test-client
  [Store Server Scripts (サーバー スクリプトの格納)]: /ja-jp/documentation/articles/mobile-services-store-scripts-source-control/
  [mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
  [Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [Windows Azure AD Graph のエラー コード]: http://msdn.microsoft.com/en-us/library/azure/hh974480.aspx
  [IsMemberOf]: http://msdn.microsoft.com/en-us/library/azure/dn151601.aspx
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
  [mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
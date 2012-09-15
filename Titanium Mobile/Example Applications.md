# サンプルアプリケーション

## Overview
ここで紹介するサンプルアプリケーションは、Titaniumでのアプリケーション構築のアプローチや、APIの使用方法を参照に役立つかもしれません。

アプリケーションはどれも、Titanium Studio上から直接インポートできます。Stdioを使わない、あるいはダウンロードを望まないユーザーのために、URLも用意されています。

## Kitchen Sink

この広大なサンプルアプリケーションは、Titanium MobileのAPIの大半を紹介しています。Titaniumのリリース2.1.1以降では、よりモダンな設計を使って完全に書き直されました。このバージョンのKitchen Sinkに含まれるサンプルは、NookやKindle Fire（または、Google APIのロケーションサービスをサポートしていないAndroidデバイス）を、まだサポートしていません。そのようなデバイス向けには、Kitchen Sinkのレガシーバージョンが存在します。

_リリース2.1.1より前のKitchen Sinkは、設計上のベストプラクティスを示してはいませんでした。そのため、実際のアプリケーションで同じ構造を採用すべきではありません。_

_Nook版のKitchen Sinkは、古いコード構造を使用しているため、実際のアプリケーションの例として使用すべきではありません。_

* Mobile - [Kitchen Sink on GitHub](http://github.com/appcelerator/KitchenSink)
* NOOK Color - [NOOK Color Kitchen Sink on Github](https://github.com/appcelerator/KitchenSink-Nook)


## RSS Reader
RSS Readerは、RSSフィードを閲覧できるTitanium Mobileのサンプルアプリです。インターネットからRSSフィードをその場で取得し、サムネイル付きの見出し一覧から、選択した記事を読むことができます。

RSS Readerに含まれる要素：

* Ti.Network.HTTPClientによるリモートデータアクセス
* CommonJSスタイルのモジュール式JavaScript
* シングルコンテキストでの複数ウインドウアプリ
* アプリケーションレベルのイベントを用いたデータとUIの疎結合
* プラットフォームネイティブのUI機能
* カスタマイズした行を持つTableView
* Androidのメニュー
* iOSのナビゲーションバーボタン
* Ti.UI.iPhone.NavigationGroupによるiOSのナビゲーションコントローラー
* Ti.UI.MobileWeb.NavigationGroup.MobileによるモバイルWebのナビゲーショングループ
* WebViewによるWebコンテンツの表示
* クロスプラットフォーム対応

### スクリーンショット
#### iOS
![](http://docs.appcelerator.com/titanium/2.1/images/download/attachments/29004877/rss_reader_ios.png)
#### Android
![](http://docs.appcelerator.com/titanium/2.1/images/download/attachments/29004877/rss_reader_android.png)

### 関連ドキュメント
* Networking
 * Guides: Working with Remote Data Sources
 * API Docs: Ti.Network.HTTPClient
 * Guides: Application level events
* Web Views
 * Guides: Integrating Web Content
 * API Docs: Ti.UI.WebView
* UI Elements
 * Guides: Android menu
 * API Docs: Ti.UI.iPhone.NavigationGroup
 * API Docs: Ti.UI.MobileWeb.NavigationGroup


## Todo List
Todo Listは、ToDo把握のためのシンプルなタブ型アプリケーションです。完了すべきタスクのリストを管理し、リストへの追加や、タスクの完了を指定できます。
このアプリでは、データベースストレージを使用しています。モバイルWebでは、Ti.Databaseがサポートサれていない場合、Properties APIを使ってローカルストレージを提供します。

Todo Listに含まれる要素：

* Ti.Databaseを使ったSQLiteによるローカルストレージ
* モバイルWebではTi.App.Properties APIを使ったローカルストレージ
* CommonJSスタイルのモジュール式JavaScript
* シングルコンテキストでの複数ウインドウアプリ
* プラットフォームネイティブのUI機能
* Androidのメニュー
* iOSのナビゲーションバーボタン
* クロスプラットフォーム対応

### スクリーンショット
#### iOS
![](http://docs.appcelerator.com/titanium/2.1/images/download/attachments/29004877/todo_ios.png)

#### Android
![](http://docs.appcelerator.com/titanium/2.1/images/download/attachments/29004877/todo_android.png)

### 関連ドキュメント

* Guides: Working with a SQLite Database
* API Docs: Ti.Database
* API Docs: Ti.App.Properties

## Geocoder
このサンプルアプリは、ネイティブマップを使用して地図を描画します。またジオコードアドレスを転送したり、地図へアノテーションを追加したりも可能です。

Geocoderに含まれる要素：

* Titanium.Mapによるネイティブマップ
* 地図へのアノテーションの追加
* Ti.Network.HTTPClientによるリモートデータアクセス
* CommonJSスタイルのモジュール式JavaScript
* クロスプラットフォーム対応

### スクリーンショット
#### iOS
![](http://docs.appcelerator.com/titanium/2.1/images/download/attachments/29004877/geocoder_ios.png)
	
#### Android
![](http://docs.appcelerator.com/titanium/2.1/images/download/attachments/29004877/geocoder_android.png)

関連ドキュメント

* Guides: Location Services
* API Docs: Titanium.Map

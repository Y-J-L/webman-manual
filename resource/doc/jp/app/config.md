# 設定ファイル

プラグインの設定は通常のWebmanプロジェクトと同じですが、プラグインの設定は通常、現在のプラグインにのみ影響します。通常、メインプロジェクトには影響しません。
例えば、`plugin.foo.app.controller_suffix`の値は通常、プラグインのコントローラーの接尾辞にのみ影響し、メインプロジェクトには影響しません。
例えば、`plugin.foo.app.controller_reuse`の値は通常、プラグインでコントローラーを再利用するかどうかにのみ影響し、メインプロジェクトには影響しません。
例えば、`plugin.foo.middleware`の値は通常、プラグインのミドルウェアにのみ影響し、メインプロジェクトには影響しません。
例えば、`plugin.foo.view`の値は通常、プラグインで使用するビューにのみ影響し、メインプロジェクトには影響しません。
例えば、`plugin.foo.container`の値は通常、プラグインで使用するコンテナにのみ影響し、メインプロジェクトには影響しません。
例えば、`plugin.foo.exception`の値は通常、プラグインの例外処理クラスにのみ影響し、メインプロジェクトには影響しません。

ただし、ルートはグローバルなため、プラグインの設定されたルートもグローバルに影響します。

## 設定の取得
特定のプラグインの設定を取得する方法は`config('plugin.{プラグイン}.{具体的な設定}');`です。例えば、`plugin/foo/config/app.php`のすべての設定を取得する方法は`config('plugin.foo.app')`です。
同様に、メインプロジェクトや他のプラグインは`config('plugin.foo.xxx')`を使用して、fooプラグインの設定を取得できます。

## サポートされていない設定
アプリケーションプラグインは`server.php`、`session.php`の設定をサポートしておらず、`app.request_class`、`app.public_path`、`app.runtime_path`の設定もサポートしていません。
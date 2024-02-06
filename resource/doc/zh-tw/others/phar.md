# phar打包

phar是PHP裡類似於JAR的一種打包檔案，你可以利用phar將你的webman專案打包成單個phar檔案，方便部署。

**在這邊非常感謝 [fuzqing](https://github.com/fuzqing) 的PR。**

> **注意**
> 需要關閉`php.ini`的phar配置選項，即設定 `phar.readonly = 0`

## 安裝命令行工具
執行 `composer require webman/console` 進行安裝。

## 設定
開啟 `config/plugin/webman/console/app.php` 檔案，設定 `'exclude_pattern'   => '#^(?!.*(composer.json|/.github/|/.idea/|/.git/|/.setting/|/runtime/|/vendor-bin/|/build/|vendor/webman/admin))(.*)$#'`，在打包時排除一些無用的目錄及檔案，避免打包體積過大。

## 打包
在webman專案根目錄執行指令 `php webman phar:pack`，會在build目錄生成一個`webman.phar`檔案。

> 打包相關配置在 `config/plugin/webman/console/app.php` 中。

## 啟動停止相關指令
**啟動**
`php webman.phar start` 或 `php webman.phar start -d`

**停止**
`php webman.phar stop`

**查看狀態**
`php webman.phar status`

**查看連線狀態**
`php webman.phar connections`

**重啟**
`php webman.phar restart` 或 `php webman.phar restart -d`

## 說明
* 執行webman.phar後會在webman.phar所在目錄生成runtime目錄，用於存放日誌等臨時檔案。

* 如果你的專案裡使用了.env檔案，需要將.env檔案放在webman.phar所在目錄。

* 如果你的業務需要上傳檔案到public目錄，同樣需要將public目錄獨立出來放在webman.phar所在目錄，這時候需要配置`config/app.php`。
```
'public_path' => base_path(false) . DIRECTORY_SEPARATOR . 'public',
```
業務可以使用輔助函數`public_path()`找到實際的public目錄位置。

* webman.phar不支援在Windows下啟用自定義進程。
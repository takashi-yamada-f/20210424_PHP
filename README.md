# 20210424_PHPの今風な？の書き方

## （１）PHPの今風をみる前に、PHPの歴史を振り返ってみましょう。

25年に渡る PHP の歴史 | JetBrains: Developer Tools for ...<br>
https://www.jetbrains.com/ja-jp/lp/php-25/

## （２）200X年代〜
HP、ブログ、次々に新しいサービスがリリースされました。<br>
PHPでたくさんのサービスが生み出された。

#### その時代を代表する企業としては「はてな」でしょうか。。<br>
https://ja.wikipedia.org/wiki/%E3%81%AF%E3%81%A6%E3%81%AA_(%E4%BC%81%E6%A5%AD)<br>
サービスのリリース、機能のリリース、半端ないです。<br>当時は、一日一機能リリースなどと言われていました。<br>

#### この頃のPHP周辺技術等は
```
バージョン管理：SVN
PHP標準ライブラリ：PEAR
コーディングルール：PEARコーディングルール準拠
開発ツール：vim、秀丸エディタ
環境：オンプレミス（実機）、さくらインターネット等のレンタルサーバ
開発環境：XAMAPPや開発サーバ
```

## （３）リーマンショック(2007)以降
あらゆるサービスがWEB化され、サービスを生み出す速度は鈍化。<br>
一時期のソーシャルゲームバブルを例外として、普通のWEBアプリケーションはサービスではシステムリニューアルはコストがかかる為、同じシステムを運用し続けることになり、<br>WEBアプリケーションは作り捨てではなく、資産となった。

#### プログラムの保守性、可読性が重視される時代に

#### 最近のPHP周辺技術等は
```
バージョン管理：git(github、gitlab)
PHP標準ライブラリ：composer
コーディングルール：PSR-2、PSR-12
開発ツール（IDE）：phpstorm、vscode
環境：AWS、オンプレは少数
開発環境：Docker、AWS
```

## （４）覚えておきたい、最近のPHPの書き方

ざっと書いてみると、こんな感じです。
``` php
<?php 
declare(strict_types=1); 
 
namespace App\Http\Controllers\Admin\Addresses; 

中略...
use Illuminate\Http\Response; 
use Illuminate\Http\Request; 
 
class AddressController extends Controller 
{ 

中略...

    /** 
     * Display a listing of the resource. 
     *   
     * @param Request $request 
     * @return Illuminate\Http\Response 
     * @throws Exception 
     */  
    public function index(Request $request): Response 
    {   
        $list = $this->addressRepo->listAddress('created_at', 'desc'); 
        if ($request->has('q')) { 
            $list = $this->addressRepo->searchAddress($request->input('q')); 
        }   
        $addresses = $list->map(function (Address $address) { 
                return $this->transformAddress($address); 
                })->all(); 
        return view( 
                'admin.addresses.list', 
                ['addresses' => $this->addressRepo->paginateArrayResults($addresses)] 
                );  
    } 
    
中略...

} 
```
https://github.com/jsdecena/laracom　のコードを素に書き足して用意しました。

### 1. 名前空間

名前空間 | PHPマニュアル<br>
https://www.php.net/manual/ja/language.namespaces.php<br>
<br>
5分で理解するPHPのnamespaceとuseの使い方<br>
https://remotestance.com/blog/3064/<br>

### 2. 型宣言、型指定

PHPでも、関数の引数の型が指定できるようになっています。<br>
PHP5.4まではself,array,callable,クラス名・インターフェイス名のみで<br>
PHP7からbool,float,int,stringの指定ができます。<br>
<br>
また、戻り値の型も指定できます。型は関数の引数と同じものが指定できます。<br>
<br>
素のままだと、型を宣言しているだけ、従来どおり柔軟に違う型を許容してしまいます。<br>
ファイルの先頭に厳密な型指定を設定すると、関数は宣言されたのと正確に一致する型のみを受け付けるようになります。
```
<?php
declare(strict_types=1);
```
PHP5.6.xからPHP7.0.xへの移行 新機能 | PHPマニュアル<br>
https://www.php.net/manual/ja/migration70.new-features.php<br>
<br>
PHPの型宣言(タイプヒンティング)<br>
https://qiita.com/buntafujikawa/items/675dec8f3f357b23da5e<br>
<br>
[PHP] 型宣言(PHP7)<br>
https://qiita.com/hikaru_/items/2c12068eafea75e51cce<br>
<br>

### 3. PSR-2、PSR-12規約
【PHP】PSR-2 Coding Style Guide（コーディングスタイルガイド）<br>
https://www.ritolab.com/entry/92<br>
<br>
PSR-2 に変わって新しく制定された PSR-12 のチェックを PHP_CodeSniffer で行う<br>
https://www.utakata.work/entry/php/phpcs-psr12-intellij-jenkins<br>
<br>
vscodeでphpcsによるPSR-2規約チェックを行ってみた - Qiita<br>
https://qiita.com/usushioxp/items/f95b4d7ddf2fac84edad<br>
<br>
### 4. 配列の省略形
未だ、array（）と書いてしまう人へ。
```
<?php
$array = array(
    "foo" => "bar",
    "bar" => "foo",
);

// PHP 5.4 以降では省略形で書きましょう。
$array = [
    "foo" => "bar",
    "bar" => "foo",
];
?>
```

### 5. エルビス演算子とNull合体演算子
他と比べると、重要度は落ちるけれど、知っておきましょう。<br>
<br>
Null 合体演算子<br>
https://www.php.net/manual/ja/migration70.new-features.php<br>
<br>
似てるようで違う、PHPのエルビス演算子とNull合体演算子<br>
https://qiita.com/jay-es/items/3b8734bc02070d074a3e<br>
<br>

## (5)IDE　何をつかっていますか？
リファクタリング機能、コード整形機能が使えるIDEも使いましょう。<br>
<br>
### 1. phpstorm
https://www.jetbrains.com/ja-jp/phpstorm/
有料。客先プロジェクトの費用で買って貰うなどして、PHPerならば、調達したい高機能IED。<br>
同時起動しなければ、１ライセンスでOKとか言ってくれます。<br>
<br>
PhpStormにPSR-2を適用する | みけぽんブログ -自称中級 ...<br>
https://mikepon.jp/phpstorm-psr-2/
<br>
### 2. VS Code
https://code.visualstudio.com/
無償だが、とても使えるIDE。<br>
<br>
VSCodeにPHP_CodeSnifferを導入する。 - Qiita<br>
https://qiita.com/k941226/items/0b8197ceefd608c91715
<br>
### 3. NetBeans
https://netbeans.apache.org/
無償。使ってる人は多くないか。<br>
<br>
## （6）おまけ
AWSを開発環境とする場合。ローカルのMACでサーバディレクトリをマウントすると楽なので、紹介しておきます。<br>
<br>
sshfsを使って踏み台サーバ経由で開発用サーバのファイルをマウントする方法<br>
https://qiita.com/satto_sann/items/76cee7b5f6a7b02e6427<br>
<br>
## （7）PHP８について
<br>
PHP Conference Japan 2020: PHPの今とこれから2020 / 廣川類さん<br>
https://www.youtube.com/watch?v=Of_UDFoZNrA&t=1649s<br>
43分くらいから、PHP8について、お話されています。<br>
<br>
PHP7.4と大きくは変わりません。<br>
<br>
PHP8はJITエンジンを使用可能になるのが、大きな変更点かと思います。<br>
簡単に言うと、JITはOPcacheのすごい奴ですが、そもそもPHP7でPHP5の倍速になっているので、小さな改善かもしれません。<br>
名前付き引数、union型はすぐに使うでしょう。<br>
<br>
PHP7.4.xからPHP8.0.xへの移行 | PHPマニュアル<br>
https://www.php.net/manual/ja/migration80.new-features.php
<br>
<br>
下位互換性のない変更点 | PHPマニュアル<br>
https://www.php.net/manual/ja/migration80.incompatible.php
<br>
「0 == ""」の結果が変わってしまうのは、旧バージョンから移行した際に要注意。<br>__autoload() も廃止になっているので、ここも旧バージョンから移行の際に要注意。<br>
<br>
以上です。

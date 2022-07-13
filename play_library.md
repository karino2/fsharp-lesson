---
title: "第一回 まずはライブラリをつついてみよう"
layout: page
---
<style>
   .column{
        padding: 0.5em 1em;
        margin: 2em 0;
        color: #5d627b;
        background: white;
        border-top: solid 5px #5d627b;
        box-shadow: 0 3px 5px rgba(0, 0, 0, 0.22);
    }
    .post-content h1 {
       font-size: 34px;
    }
</style>

F# での基本的な作業の進め方に慣れつつ、あとで使う事になる準備として、
ライブラリをつついてみたいと思います。

## セットアップ

NYI: dotnet coreでVSCodeでIonideのセットアップをしよう、的な事を誰かが書いてくれる。

## Deedleをつついてみよう

Deedleをつついてみましょう。

### fsharp-lesssonでのフォルダとブランチのルール

初回なので、少しfsharp-lesson全般の話をここでします。

まず作業は、fsharp-lessonのレポジトリ、[https://github.com/karino2/fsharp-lesson](https://github.com/karino2/fsharp-lesson)を各自がgithub上でforkして、それをローカルにcloneしたフォルダの上で作業する事にします。

ソースコードはsourcesの下のサブディレクトリに置く事にします。
第一回だったら、`sources/play_library/` というディレクトリがあると思うので、この下にそれぞれディレクトリをほって作業します。

また、少し通常のブランチの使い方と違いますが、添削の都合で各作業はブランチを切る事にします。
第一回のブランチ名は全て `play_library/` を前につけて、あとは数女の通し番号とどこかを表す名前を適当につけます。

例えばDeedleをいじってみるというこのブランチは`play_library/1_playdeedle` としましょう。
次のArguは`play_library/2_helloargu` とします。（2のラベル名はあとで変えるかも）

### PlayDeedleのブランチとプロジェクトの作成

それではまずはブランチとプロジェクトを作成してみます。
Deedleをいじってみるのはfsx上で行うのでプロジェクト無しでも良いのですが、
今後の開発の練習という事でPlayDeedleというプロジェクトを作る事にします。

まずは上記で説明した通り、`play_library/1_playdeedle`というブランチを切り、以後このブランチで作業します。

次にプロジェクトを作成します。場所は`sources/play_library/`の下に`PlayDeedle`というディレクトリが作られるようにプロジェクトを作ります。
以下のように実行しましょう。

```
$ cd sources/play_library
$ dotnet new console -lang "F#" -o PlayDeedle
```

この時にPlayDeedleというディレクトリは勝手に掘られるので、そのディレクトリは掘らないのがコツです。

プロジェクトの作り方の詳細については以下のドキュメントを見ましょう。

[Get Started with F# in Visual Studio Code - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/get-started/get-started-vscode)

### fsxでDeedleをつついてみる

プロジェクトを作ったら、最初にするのは、私の場合はScratch.fsxというファイルを作ってVSCodeでこのプロジェクトを開く事です。
fsharp-lessonでもそうしてみましょう。

```
$ cd PlayDeedle
$ touch Scratch.fsx
$ code .
```

これでVSCodeが開くはずです。

Scratch.fsxはとりあえず実験用の書き捨てのコードを置く場所にしています。

VSCode上でScratch.fsxを開いて、以下のように書いてみましょう。

```
#r "nuget:Deedle"

open Deedle
```

そしてそれぞれの行の終わりで Alt+Enterを押します（その行を実行というショートカットです）

この時、最初の実行ではDeedleという所が破線のままでエラーっぽく表示されたままになっているかもしれません。
これはfsxではちょくちょくある事なので、実行したあとに「Windowの再読み込み」というのをやります。

せっかくなのでもし破線が出てない人も、ここでWindowを再読み込みしてみましょう。

Windowの再読み込みは、「Cmd+Shift+P」を押してから、「Reload」と入力するとそれっぽい候補が出ると思うのでそれを選んでEnterを押して下さい。
なおCmdはWindowsだとCtrlになると思います（以後全部Macのショートカットで書くので適宜読み替えて下さい）

![reload_window](reload_window.png)

こうするとウィンドウごと再読み込みされて、破線が無くなると思います。

なお、再読み込みするとまっさらな状態になるので、先程と同様に、書いたスクリプトの2行のそれぞれの末尾でAlt+Enterして下さい。

そのあとに以下のように入力してAlt+Enterしてみてください。

```
Frame.ReadCsv "../../data/シラバス.csv"
```

無事、下のウィンドウになにかずらずらと、csvの内容を読んだDataFrameっぽいものが出ているでしょうか？

（たまに文字列が化ける事があるのでその場合はもう一回Alt+Enterで実行してください）

ではここまでにやった事を簡単に解説していきます。

### fsharp scriptとfsxについて

F# には.fsのファイルと.fsxのファイルがあります。以下簡単に両者について説明しておきます。

拡張子が.fsのファイルは、通常のF#のプログラムを置く所で、XXX.fsprojに管理されています（または管理するようにfsprojを変更する必要があります）。
これはいわゆる「本番」のコードを置く場所で、これをビルドする事で実行バイナリを作るものです。

一方で拡張子が.fsxのファイルはfsharp scriptというスクリプトを置くファイルです。

fsharp scriptはほとんど通常のF#のコードと同じですが、パケージのロードなどの拡張が入っていたりと微妙に違う所もあります。

Ionideでは.fsxファイル上でAlt+Enterすると裏でfsharp scriptのインタープリタが立ち上がって、そこに選択したテキストを送る感じの挙動になっています。
シンタックスハイライトなどはファイルを先頭から見ていっておかしな所を指摘しますが、インタープリタとしては送られてきたテキストを順番に見ていって問題が無ければ正しく動きます。

例えば、以下のようにopen文と使う文を逆にすると、

```
Frame.ReadCsv "../../data/シラバス.csv"

open Deedle
```

シンタックスハイライトとしてはエラーっぽい表示がされますが、
先にopen文の上でAlt+Enterしたあとにカーソルを上に戻してFrameの行でAlt+Enterをしてやれば正しく動きます。（もちろんそういう事はあとで見た時にどう実行するかが分かりにくいのでなるべく避けた方がいいですが）

また、fsxファイル上でちょっとなにかを書いては実行してそれを元に修正して、またちょっと変更して実行して、と繰り返していくと、
裏のインタープリタに何が実行されている状態かわからなくなる事もありますし、なにか変な状態になってしまう事もあります。
そういう時は先程説明した「Reload Window」でインタープリタの状態をリセットし、また最初から実行しましょう。

基本的には触っていきながら慣れていけばいいと思いますが、もっと詳しく知りたい方は以下を見るといいでしょう。

- [F# Interactive (dotnet) Reference - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/fsharp/tools/fsharp-interactive/)
- [F# でのfsxベースの開発 - なーんだ、ただの水たまりじゃないか](https://karino2.github.io/2021/02/06/fsx_eval_based_dev.html)

### fsx上でのNuGetパッケージのロード

先程、以下のようなスクリプトを実行しました。

```
#r "nuget:Deedle"
```

これはnugetというパッケージマネージャ上からDeedleの最新版を取ってきてそのパッケージを「触れる状態」にしてくれます。
これを実行するまではVSCodeはDeedleというものを何も知らないので、open文などがシンタックスエラーになっていました。

さて、ここに書く文は、Nugetのサイトを見るとわかります。
基本的には"Deedle NuGet"でググって、引っかかったnugetのサイトを見れば良い。
例えば今検索して引っかかったのが以下。

[NuGet Gallery - Deedle 2.5.0](https://www.nuget.org/packages/Deedle)

バージョンが幾つかは検索した時期で違うと思う。

で、このページには様々な環境でのライブラリの使い方が書いてあるのだけれど、`Script & Interactive` と書いてあるタブを見ると以下のように書いてある。

```
> #r "nuget: Deedle, 2.5.0"
```

基本的にはこれがfsharp script上での書き方なのだけれど、自分は書き捨てスクリプトでいろいろ試したい時はバージョン指定しない事が多い。

そこでカンマのあとを削除して、以下の文が出来る。

```
#r "nuget: Deedle"
```

なお、 `.NET CLI` というタブを見るとfsprojへの追加方法が書いてある。これは後ほど出てきます。

### Deedle入門




## Arguをつついてみよう

## FParseをつついてみよう



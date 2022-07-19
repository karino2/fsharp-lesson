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

[Install F# with Visual Studio Code](https://docs.microsoft.com/en-us/dotnet/fsharp/get-started/install-fsharp#install-f-with-visual-studio-code)に従って.NET、VSCode、Ionideのインストールを行ってください。

## Deedleをつついてみよう

Deedleをつついてみましょう。

### fsharp-lesssonでのフォルダとブランチのルール

初回なので、少しfsharp-lesson全般の話をここでします。

まず作業は、fsharp-lessonのレポジトリ、[https://github.com/karino2/fsharp-lesson](https://github.com/karino2/fsharp-lesson)を各自がgithub上でforkして、それをローカルにcloneしたフォルダの上で作業する事にします。

ソースコードはsourcesの下のサブディレクトリに置く事にします。
第一回だったら、`sources/play_library/` というディレクトリがあると思うので、この下にそれぞれディレクトリをほって作業します。

また、少し通常のブランチの使い方と違いますが、添削の都合で各作業はブランチを切る事にします。
第一回のブランチ名は全て `play_library/` を前につけて、あとは数字の通し番号とどこかを表す名前を適当につけます。

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

もっと見やすい方法はあとで示すので現時点ではとりあえずなにかそれっぽいのが表示される所までいったらOKです。

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

さて、ここで使ったDeedleというライブラリの解説も軽くしておきましょう。
Deedleは、pythonでいう所のpandasみたいなものです。
ただpandasに比べてもうちょっと型に厳格な印象です。
良い事もあれば小回りが効かないと思う事もあるでしょう。

Deedleに関する情報としては、公式ドキュメントがよく書けていると思います。

[Deedle: Exploratory data library for .NET](https://fslab.org/Deedle/)

またこのページから辿れるチュートリアル、[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)もよく書けています。

fsharp-lessonとしてはこれらを全て読む必要は無いと思いますが、最初の方くらいはちらっと見ておいてからこの先を読む方が分かりやすいかもしれません。

### 対話的に処理している時の整形について

さて、それでは実際にcsvをロードしてちょっとした処理をしてみましょう。

まず、現状は表示があまり見やすくありません。
対話的に処理をする時には以下を実行すると見やすくなります。

```
#load "Deedle.fsx"
```

これが何をやっているかは私もしりません。
ただこれをやるとFrameをロードしたりした時に表示が綺麗になります。

以下を試してみてください。

```
#load "Deedle.fsx"
Frame.ReadCsv "../../data/シラバス.csv"
```

また、この"Deedle.fsx"をロードすると、本来何も出力していないような処理、例えば以下のような処理でも内容が出力されます。

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
```

これは便利な事もあれば不便な事もあるので、不便な時にはまたReload Windowした上でDeedle.fsxをロードせずに作業してください。

"Deedle.fsx"をロードしなくてもちょっと整形した結果を見てみたい、という場合には、
FrameのインスタンスのPrintメソッドを呼ぶと同じ出力が得られます。

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
df.Print()
```

### 簡単なフィルタリングをしてみよう

ではこのシラバスのdf

```
let df = Frame.ReadCsv "../../data/シラバス.csv"
```

から、専門が数学だけの行を取り出してみましょう。


フィルタに関しては、[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)の「Projection and filtering」という所に記述がありますが、
以下のようになっています。

```
joinedOpens.RowsDense
|> Series.filterValues (fun row -> row?MsftOpen > row?FbOpen)
```

このSeries.filterValuesというのを使えば良さそうですが、それに渡す関数が問題です。
以下のようになっています。

```
fun row -> row?MsftOpen > row?FbOpen
```

MsftOpenとFbOpenはカラムのタイトルのようです。
この間の?はクエスチョンマークオペレータというもので、詳細はあとで解説します。

ただ我々の例ではカラム名が日本語なので、このクエスチョンマークオペレータというのは使えそうにありません。

答えの前に、こういう時にどうやって試行錯誤して答えにたどり着くのか？という話をしたいと思います。

もともとのやりたい事に戻れば、たぶん以下のようになるのはわかると思います。

```
df.RowsDense
|> Series.filterVlaues( fun row -> ???ここに「専門」が「数学」と書きたい???)
```

そこでrowに何が出来るかを調べたい。
その為にはとりあえずrowを一つ取り出してみるのがいいと思います。
という事で取り出す方法を探してみる。

まず、`df.`と入力してみる。
するとプロパティやメソッドの一覧が出ますが、その中から使えそうなプロパティを探すとRowsかRowsDenseが見つかる。

まずはdf.Rowsを見てみる。以下を実行してみます。

```
df.Rows
```

すると `RowSeries<int,string>` というのがかえってくる模様。このドキュメントを見てみてもいいのだけれど、もうちょっと適当につついてみる。

次は `df.Rows.` と入力して、Rowsの使えるプロパティはメソッドの一覧を出してみましょう。

するとGetとGetAtというのが使えそうに見える。このメソッドの違いはこの例は偶然ややこしいので後回しにして、どちから好きなメソッド、ここではGetAtを使いましょう。
たぶんこれで指定した位置のRowが取れそうです。

```
df.Rows.GetAt(0)
```

これを実行したら、 `ObjectSeries<string>` というものが取れました。これがrowの正体のようです。
そこでこれを変数に入れて、いろいろつついてみます。

```
let row = df.Rows.GetAt(0)
```

として、`row.`と入力して何が使えるかをいろいろつついてみます。

### 課題1: GetとGetAsの違いを調べよう。

いろいろつついてみた所、どうもrow.Getとrow.GetAsのどちらかでカラムが取れそうに見えます。
そこでこの２つを実行してみましょう。

まず以下を実行してみます。

```
row.Get("専門")
```

どのような表示がされるか見てみましょう。

次にGetAsを読んでみるとエラーが出ると思います。

```
row.GetAs("専門")
```

このエラーメッセージはどういう意味でしょう？gitterで私に説明してください。

次に以下のように実行すると正しく実行されます。

```
row.GetAs<string>("専門")
```

この文と先ほどのGetの結果はどう違うでしょうか？gitterで私に説明してください。

### 課題2: 専門が数学の行だけを残そう

以下の???を書いて、専門が数学の行だけ取り出して下さい。

```
df.RowsDense
|> Series.filterValues(fun row -> ???)
```

これはScratch.fsxに書いてcommitしてpushし、gitterで私にgithub上のリンクを送って下さい。
なお、次の課題3も一緒にやってしまっても良いです。

ちなみにこのように複数行にわたったスクリプトを実行する場合はマウスやShift+矢印キーでこの二行を選択して、Alt+Enterします。
なお、検索の入力フォームが出てるとAlt+Enterが奪われて実行出来ないので注意しましょう（何度もやってイラつく…）

### 課題3: 専門が数学の行だけを持ったFrameを作ろう

課題2では、型がSeiriesのSeriesになっていると思います。
これをFrameにしてください。

[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)の「Creating series and frames」を見ながら適当に試せば出来るでしょう。

これもcommitしてpushして私に見せてください。

### 課題4: 場所と学年だけのFrameを作ろう

これも[Deedle in 10 minutes using F#](https://fslab.org/Deedle/tutorial.html)のProjection and filteringを参考に（joinedOpensを作っているあたり）


### 課題5: フィルタとプロジェクションを関数にしよう

rowを引数にboolを返す関数を引数にとってフィルタしたFrameを返すfilterと、
カラムの名前のリストを引数にとってそのカラムだけを含んだFrameを返すproject関数を作ろう。


## Arguをつついてみよう

fsxでDeedleをつつくのはだいたいわかったと思うので、次は.fsの方を見ていきます。ついでにArguというライブラリをつついてみます。
ブランチ名は `play_library/2_playargu` で行きましょう。

### プロジェクトを作って実行しよう

前回同様コマンドラインから `dotnet new` でPlayArguというプロジェクトを作ってください。
そして出来たPlayArguをVSCodeで開く所までは前回と同様です。

ただ今回はプロジェクトを生成した時に一緒に作られるProgram.fsを見てみましょう。
以下のようになっていると思います。

```
// For more information see https://aka.ms/fsharp-console-apps
printfn "Hello from F#"
```

このurlを開いてみるのも有益ですが、まずは実行してみましょう。
このプロジェクトまでターミナルからcdして、以下のように実行します。

```
$ dotnet run
```

すると少しまったあとに、以下のように出力される事でしょう。

```
Hello from F#
```

これがProgram.fsに書かれている内容なのはまぁいいでしょう。
試しに出力される文を`Hello World`になるように変更して実行してみてください。

### dotnet buildとfsproj

次に `dotnet run` を実行した時に生成されるファイルを見ます。

binというディレクトリが作られているはずです。
このbinの下の、`bin/Debug/net6.0/` という所を見ると、なんだか一杯ファイルがあると思います。

ここにある `PlayArgu` というファイルを実行すると、同じ結果になるはずです。

```
$ bin/Debug/net6.0/PlayArgu
Hello from F#
```

`dotnet run` というコマンドは

1. PlayArguという実行ファイル（及び関連するdllや設定ファイルなど）を作る
2. PlayArguを実行する

という２つの事をやってくれるコマンドです。

1だけを実行する別のコマンドもあって、それは`dotnet build`です。

```
$ rm -r bin
$ dotnet build
$ bin/Debug/net6.0/PlayArgu
Hello from F#
```

dotnet buildというのは、現在のディレクトリにあるfsprojファイル（この場合はPlayArgu.fsprojという名前）に書かれている.fsのファイルを全部まとめて実行ファイルに変換する、という事をしてくれます。

こちらがF# の正規（？）の開発方法と思う。

### dotnet publishの話を少しだけ

なお、dotnet buildで作ったPlayArguバイナリは、同じフォルダにあるPlayArgu.dllやそのほかいろいろな物に依存しているので、PlayArguという実行ファイルをコピーして違う所に持っていくだけでは動きません。

```
$ cp bin/Debug/net6.0/PlayArgu ./
$ ./PlayArgu
The application to execute does not exist: 'XXXX/fsharp-lesson/sources/play_library/PlayArgu/PlayArgu.dll'.
```

これらのファイルがそれぞれ何なのかはdotnetの方の話になりますが、コンソールアプリで使っている分にはあまり知る必要も無いでしょう。

よその場所に持っていって実行する場合はpublishというのを実行する必要があります。
Macの場合は以下のように実行します。(他のOSについては、`-r <RID>`で指定する[Runtime Identifier (RID)](https://docs.microsoft.com/en-us/dotnet/core/rid-catalog)を変えて下さい。)

```
$ dotnet publish -r osx-x64 -p:PublishSingleFile=true
$ cp bin/Debug/net6.0/osx-x64/publish/PlayArgu ./
$ ./PlayArgu
Hello from F#
```

self-containedもつけろとかワーニングは出ますが動くはずです。
なお、この時も実行するマシンにはdotnet runtimeが入っている必要があります。ランタイムもくっつけて一つにする方法などもありますが、
このシリーズでは使わないのでこのくらいにしておきます。

publishについては以下に詳しく書いてありますが、

[dotnet publish command - .NET CLI - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish)

結構長いので必要になるまでは読まなくて良いでしょう。

### 課題6: オプションhelloを処理

とりあえずArguは使わずに、引数が `-hello` だったら"Hello World"とコンソールに出力し、
それ以外なら "I don't know"と出力するようにProgram.fsを変更して動作を確認しましょう。

以下のようになるようにします。

```
$ dotnet run -hello
Hello World
$ dotnet run
I don't know
```

これを２つの方法でやってみます。

1. `Environment.GetCommandLineArgs()`を使う方法
2. `[<EntryPoint>]`を使う方法

この時に、Program.fsに書かれているリンク先を見るとヒントになります。

[コンソール アプリケーションと明示的なエントリ ポイント - F# - Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/fsharp/language-reference/functions/entry-point)

まず1で実現してcommitし、次に2に変更してcommitしてgithubの履歴のリンクをgitterに貼って下さい。

### VSCodeからの実行

次にVSCodeからの実行方法を見てみます。

- とりあえずF5押す
  - この２つのステップが上手く行かない時は画面左端の三角と虫のアイコンをクリックして、「launch.jsonファイルを作成します」を選ぶ
- Blazor WebAssembly Debugを選ぶ（とりあえずlaunch.jsonが作られるものならなんでも良い）
- launch.jsonを以下のように書き換える

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": ".NET Core Launch (console)",
      "type": "coreclr",
      "request": "launch",
      "preLaunchTask": "Build: PlayArgu.fsproj",
      "program": "${workspaceFolder}/bin/Debug/net6.0/PlayArgu.dll",
      "args": ["-hello"],
      "cwd": "${workspaceFolder}",
      "stopAtEntry": false,
      "console": "internalConsole"
    }
  ]
}
```

- F5をもう一度押すと"Build: PlayArgu.fsprojが見つけられませんでした”と言われてダイアログが出るので、そこからタスクの構成を選ぶ
- Build: PlayArgu.fsprojを選ぶ
- task.jsonが生成されるので保存する
- F5

これでデバッグ実行が出来ます。Program.fsの適当な行の上でF9を押すとブレークポイントがはれるので、そのあとF5するとそこで止まると思います。
F10で次の行に進む、F11でステップイン（後述）、F5で次のブレークポイントまで進みます。
デバッガの使い方などは進めていく過程で学んでいくのがいいと思います。

上記のlaunch.jsonのポイントとしては、以下の点に注目しておきましょう。

- preLaunchTaskにビルドの名前を書く（fsprojの名前はプロジェクトごとに変える）
- programの行も生成されるdllのパスに変える
- argsは必要に応じて足す（ここに足したものがコマンドライン引数として渡される。上記の例では"-hello"を渡している）


### Arguをつついてみよう

では次に、fsprojにArguというライブラリを追加して使ってみましょう。
Arguはコマンドラインの引数のパーサーです。

[Argu: Introduction](https://fsprojects.github.io/Argu/)

このIntroductionやそこからリンクされている[Tutorial](https://fsprojects.github.io/Argu/tutorial.html)を見ればだいたい全てがわかるのですが、
割と簡単なものなのでとりあえず触ってみてどういうものか先に理解する方が手早いでしょう。

とりあえずいつものようにScratch.fsxというファイルを作ってつついてみます。
まずはNuGetのサイトで探します。

[NuGet Gallery - Argu](https://www.nuget.org/packages/Argu)

```
#r "nuget: Argu"
```

を実行すれば良さそう。

そして一番簡単な例として、以下のようなコードを実行してみてください。

```
open Argu

type Arguments =
    | Hoge
    | Foo of path:string

    interface IArgParserTemplate with
      member s.Usage =
        match s with
        | Hoge -> "print Hello World"
        | Foo _ -> "print Bar with arg"

let parser = ArgumentParser.Create<Arguments>(programName="hogehoge")

parser.PrintUsage()
```

IArgParserTemplateというインターフェースの定義が5行もあるのでぎょっとしますが、これはヘルプに関する記述をしているだけなので大した事はありません。
ヘルプの内容と、ArgumentsのUnion定義を見比べると、だいたいどういうものかわかるでしょう。

次に以下を実行してみます。

```
parser.Parse[| "--hoge" |]
parser.Parse[| "--foo" |]
parser.Parse[| "--foo"; "fugafuga" |]
```

なお、`[|`と`|]`でくくって中身をセミコロンで区切るのは、F#では配列の記法です。
普通リストしか使わないので見かけないかもしれませんが、引数のパーサーなのでmainに渡ってくる文字列の配列がそのまま渡せるようになっている為、
こうして実験でつつく時は配列を渡す必要があります。

上記の例だと、二番目はパースエラーに、1番目と3番目はなにかパースが成功したっぽく見えます。

### Arguのパース結果をつつく

では結果を変数に入れて少しつついてみましょう。

```
let res = parser.Parse[| "--foo"; "ikaika" |]

res.Contains Hoge
res.Contains Foo
```

ContainsのあとにはUnionで定義した値がそのまま使えるようです。
これでif文で何が渡されたかを判断出来そう。

さらにfooの時の引数を取得する為には、以下のようにGetResultを実行します。

```
res.GetResult(Foo)
```

引数は複数同時に指定出来るので、どのオプションの引数かを指定する必要があります。

```
let res = parser.Parse[| "--hoge"; "--foo"; "ikaika" |]
```

などに対してもやってみてください。


### 課題7: -hello をArgu化しよう

課題6でやった `-hello` の処理をArguで実装しなおしましょう。PlayArguプロジェクトで作業を続けます。

- fsporjへの追加は `dotnet add package Argu`
- ArguのパーサーへはEntryPointの引数をそのまま渡せば良い
- `--hello` を `-hello` にするのは `[<CliPrefix(CliPrefix.Dash)>]` をtypeにつける

あたりをヒントにやってみてください。

### 課題8: PlayDeedleのprojectとfilterをArguでコマンドライン化

`--project "場所" "学年"` みたいなオプションと、 `--filter "専門" "数学"` みたいなオプションを実装しましょう。
csvのパスは決め打ちで開いてしまっていいです。

PlayArguのプロジェクトに `dotnet add` でDeedleも追加して、課題3や課題5のコードを持ってきて修正してください。


## FParsecをつついてみよう

ライブラリをつついてみよう、第三弾はFParsecです。

- [stephan-tolksdorf/fparsec: A parser combinator library for F#](https://github.com/stephan-tolksdorf/fparsec)
- [Tutorial](http://www.quanttec.com/fparsec/tutorial.html)
- [fparsec/Samples at master · stephan-tolksdorf/fparsec](https://github.com/stephan-tolksdorf/fparsec/tree/master/Samples)

FParsecはいわゆるパーサーコンビネーターという奴ですね。パーサーのライブラリ。
少しいじったあとにチュートリアルを軽く読んで、あとはサンプルを見ればだいたいの事はわかると思う。
このページの最後に参考文献へのリンクも貼っておきますが、まずはいじりつつ必要な事だけ見ていくのが良いと思います。

FParsec入門してprojectとfilterを`project([場所], [学年])`みたいなのと、`filter([専門] = "数学")` みたいなのをパースして実行する、というのを実装してみましょう。

### まずはいじってみる

いつものように、`play_library/3_playfparsec` というブランチを作り、
PlayFParsecというプロジェクトを作り、そしてScratch.fsxを足します。

そして以下のように書きます。

```
#r "nuget: FParsec"
open FParsec

run pfloat "1.25"
```

これでとりあえず動くと思います。

なお、以下のように書くとエラーが出ると思いますが、

```
let ws = spaces
let float_ws = pfloat .>> ws
```

その次に以下の行を足すとエラーが消えるはずです。

```
run float_ws "1.25 "
```

これはFParsecをfsharp scriptで試す時の落とし穴で、使う側のコードが無いと型が確定しないのでこうなります。
とりあえず開発の途中の段階ではrunをどこかに書いておくようにするといいでしょう。

### 課題9: チュートリアルを4.6（floatのリストのパース）までやってみる

とりあえずチュートリアルを4.6まで進めてみましょう。

[Tutorial](http://www.quanttec.com/fparsec/tutorial.html)

これはcommitはして欲しいですが、私に見せなくてもいいです。

### projectのパースをする

次に、 `project([場所], [学年])` をパースしてみましょう。
一気にやるのはちょっと大変なので順番に進めます。

### 課題10: カラム名のパーサーを書こう

まずはカラム名のパースです。

`[場所]`

のパースを考えます。チュートリアルの4.3と似ていますが、中身がpfloatじゃないですね。
`[`から始まり、`]` まで。変なエスケープとかは考えないでいいでしょう。

チュートリアルの4.7のstringLiteralのパーサーがこれをもっと複雑にした事をしているので、それを見つつ書いてみましょう。
名前はpColumnにしますか。

ここまで書けたら一旦見せて下さい。

### パーサーと文法

さて、ここまでなんとなくで触ってきたパーサーというものについて、簡単に解説したいと思います。
その為には、まず文法という話をする必要があります。

例えば以下の２つの式を見て下さい。

```
sin(3.5)
sin(2*3.5+0.3)
```

両者はカッコの中が違うけれど、違うのはカッコの中だけでsinという処理自体には違いが無い、というのは人間ならわかると思います。
そしてカッコの中にはなにか「評価」をすると数値になる式ならなんでも入れられるだろう、という事もわかります。
例えば`sin(2*3.5-3.0/2)`と先ほどの文には無い`-`や`/`があっても、人間が見えればこれが問題の無い文だと解釈出来ます。

でもプログラムに、「何が正しくて何が間違っているのか」を伝えるのはなかなか難しい。
プログラムには、厳密に指定するなにかが必要です。

こうした問題で正しい事、及びその解釈を伝えるものを、「文法」と呼びます。BNFの記法を適当に拡張したものがよく使われます。

文法では、構成要素を再帰的に記述します。
たとえばよくあるのは以下。

```
stmt = funcall
     | expr

funcall = identifier '('  arglist ')'

arglist = expr
        | expr ',' arglist

expr = term '+' expr
     | term '-'  expr
     | term

term = factor '*' term
     | factor '/' term
     | factor

factor = number
       | identifier
```

ここではnumberとかidentifierは最初から与えられるものという前提になっています。
通常numberはintかfloatっぽい文字列、identifierは`[a-zA-Z_]`で始まってアルファベットと数字が続くようなものと定義される事が多いです。

縦棒で並べたものが「or」という意味で、並べたどれかのうちの一つ、という事を意味します。例えば、以下の場合

```
factor = number
       | identifier
```

「factorとは、numberまたはidentifier」と読みます。


下から順番に見ていくと、factorというのが数字か文字（変数）、で、掛け算割り算の項がtermとして定義されて、
exprとはtermを足したり引いたりしたもの、となっています。

このように、最初から与えられる「number」とか「ideintifier」などを組み合わせて、再帰的に構成要素を定義していって、
最終的にパース対象全体を定義するのが文法というものです。

このような記述と、`"sin(2*3.5+0.3)"`という文字列を比較してあっているかどうかを判定して、各文字を構成要素に分解するのがパーサーと呼ばれるものです。

これだとfuncallがexprにならないので、`sin( sin(3.0) )`みたいなのがエラーになってしまうので、普通はもうちょっと複雑になりますが、文法というものの基本を理解するにはこの程度で十分でしょう。

### パーサーの気分で考える

このように定義されたstmtをパースするparseStmtというものが何らかの方法で文法から生成出来たとします（これはパーサージェネレータやパーサーコンビネータと言われるものを使って作れる）。

このparseStmtは、これを上から順番に探していきます。（この探し方によってパーサーの種類がいろいろあるのですが、ここではFParsecを前提にそうした解説を省きます。厳密な事を知りたい人はコンパイラの教科書でちゃんと勉強しましょう）。

つまり`"sin(2*3.5+0.3)"`という文が与えられると、まずstmtを見る。

すると、funcallかexprのどっちかだな、という事が文法に書かれている。
で、funcallを見るとidentifierと'('で始まる、と書いてあるので、とりあえずfuncallだと思ってパースを進める、という風に進みます。
基本的には２つの字句（この場合は`sin`と`(`）までを見てこれらの分岐を判断する、と思っておくと、だいたいの挙動は理解できると思います。
字句とはなんぞやとかはあまり真面目に解説する気が無いので、それっぽい単位と思っておいてください。（なおこの2の数字をどこまで増やすかで文法の種類が決まるが、我らは2（つまり先読み1）しか使わない）

次に`3+4*2`の例を考えてみる。

parseStmtはfuncallかexprのどちらかだ、と書いてあるので、最初の２つの字句を見ると、`3`と`*`になっている。
funallのルールを見ると`identifier`と`(`と書いてあって、普通3はidentifierとはパースされないように作るので、
これは駄目っぽい（どっちにしろ`(`はマッチしてないので駄目）。

という事でexprの方に行く。
exprの方は〜と見ていくと無事最後まで解釈できるので、これはパースが成功した事になります。

### 文法の書き方の注意

さて、ここまでの話で、文法的には問題無いが、パーサーには苦手な文法の書き方があります。
最初の数個を読んで判断出来ないような文法は苦手です。

例えば以下のexprを、

```
expr = term '+' expr
...
```

以下のようにtermとexprを入れ替えても、人間には意味がわかります。

```
expr = expr '+' term
...
```

ですが、パーサーはこういうのは苦手です。左辺のexprが右辺の「一番左」で再帰しているので、「左再帰」とか呼ばれます。

文法を書く時には、「この|は一つの字句を先読みする事で判断できるか？」と考えながら書くのが大切です。
なるべく簡単にどれか確定するように記述してある方が、デバッグも簡単ですしパーサーも得意です。

### パーサーとはなにか（その2）

さて、パーサーとは文法を元に、文字列を解釈してあっているかどうかを調べるものだと言いましたが、これだと少し不完全です。
プログラマとしては、合っているかどうかだけじゃなくて、各構成要素をもらって、それを処理したい。

だからパーサーとは文字列を解釈して構成要素に分解して返すもの…と言いたい所だけれど、実際はもうちょっと複雑です。

先程の例を見てみましょう。`sin(3+5*2)`という文をパースしたとします。
これをパースした結果は何が返るべきでしょうか？

そもそも、`3+5*2` 文法のうちどの構成要素でしょうか？

sinの引数なのでarglistのように思います。
でもarglistには複数の構成要素があり、そのうちのどれか、という事が知りたい。

そのうちのどれかというと、expr一つのパターンにマッチするのでexprという事になります。

つまり、arglistであると同時にexprでもあるのです。

文法とは定義のされ方から、一つの部分が複数の文法要素のネストしたものになっています。

そこでこうした構造をそのまま返そうとすると、ツリー構造となります。これをExpression Treeと言い、
パーサーの多くは何らかの方法でExpression Treeを返すモードもあります。

ただ普通のパーサーは、各パースの構成要素が確定した段階で、なにかを返すという関数を外から指定する事が出来ます。
だからパーサーというのは、「文字列を読んで構成要素にマッチングしていき、各構成要素が確定した時に指定された値を返していく」ものとなります。

だからパーサーの記述には「文法」と、「それがマッチした時に返すもの」の２つを考えていく必要があります。

パーサーコンビネータの各パーサーを考える時には、「XXという文字列にマッチしてYYを返すもの」という風に、
マッチする対象と返すものの２つに着目すると良いでしょう。

以下少し練習してみます。

### pfloatとfloat_wsを見直してみる

冒頭でやった、以下の文を見てみましょう。

```
let ws = spaces
let float_ws = pfloat .>> ws

run float_ws "1.25 "
```

まずpfloatとはなにかを考えます。pfloatは「"123.45"のような文字列にマッチして、123.45というfloatを返すパーサー」となります。

float_wsはなにか、というと、「"123.45  "のような文字列にマッチして、123.45というfloatを返すパーサー」になります。
この返すのがなにか、というのを意識する必要があります。

どこでそれが決まっているのかを見ると、以下の文です。

```
let float_ws = pfloat .>> ws
```

ここで`.>>`というのが出てきています。これは「左と右のパースを行って、左側の結果を返す」という「パーサー」を新たに作る、という意味です。

マッチングと返すものを別々に考えるのがコツです。
マッチングは「pfloatとws」、返すものは「pfloatと同じ結果」です。

以上をふまえて、課題10で自分が書いたものも何とマッチングして何を返すのか、考えてみてください。

### パーサーコンビネータとはなにか

なにか書く。


### パーサーの参考文献

文法とかパーサーをより本格的に学びたいなら、コンパイラのコースや教科書を読むのが良いでしょう。

[Compilers - edX](https://www.edx.org/course/compilers)

無料のオンラインコースならこちら。Week2〜Week4が文法とパーサーの真面目な解説です。ただ期限が来るとコンテンツにアクセスできなくなる（もう一回enrollすれば見れるんだけど）のがちょっとかったるい。
自分は別の所で勉強しているので復習したい所だけをたまにenrollしては見ているが、なかなか出来は良いと思う。

本なら虎本が自分はよく参照する。

<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=karino203-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B00D2WQAE8&linkId=ed4cca3cbb4a5c8c72621a31d3dceb24"></iframe>

これ。コンパイラの教科書としては虎本とDragon bookの２つが有名だが、Dragon bookはkindleになってないので、自分は主にこの辺を調べたくなったら手元の虎本を参照しているし、これで十分と思う。
3章のParsingは十分以上の内容になっていると思う（読む時は2のLexical Analysisから読む方が良いかも）。
日本語版もあるけれど読んだ事無いので英語版をおすすめしておく。
別に日本語版でもいいかもしれない。知らない。

どちらも割と本格的な内容なので、ここまで知らんでもなぁ、というものも多く含まれていると個人的には思います。
理論的な所に分量が割かれ過ぎていて、複雑で使われていないが表現力が高いものとかに多くの紙面が割かれ、
逆に実際の実装ではもっと改善が進んでいる話題が入っていない。

パースは割とこうした教科書の話題からはみ出た重要な話題が近年は技術ブログなどでちょこちょこ出てきますが、
そういうのは必要になったら調べるくらいで十分な気もする。

FParsecとかパーサーコンビネータをもっとちゃんと勉強したい人向けにも、参考文献リンクを貼っておきます

- [The "Understanding Parser Combinators" Series · F# for Fun and Profit](https://swlaschin.gitbooks.io/fsharpforfunandprofit/content/series/understanding-parser-combinators.html) FParsecを意識した解説になっているので連続性はある。ただし具体的なコードが多い分、本質が見えにくい気もする。
- [Monadic Parser Combinators.pdf](http://www.cs.nott.ac.uk/~pszgmh/monparsing.pdf) 理論的な事だけ知りたいならこれが良いが、実装言語がGoferとか言われても…という気はするが、Haskellの入門くらいかじっておけば雰囲気で読める。数学的な記述に近くて本質が分かりやすい。著者のGraham HuttonとErik Meijerは数年後に[parsec-paper-letter.pdf](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/parsec-paper-letter.pdf)というのも書いているが、勉強目的なら最初のだけで良いでしょう。関連文献などもあるし論文の体裁が得意な機械学習屋はこういう方が好みかもしれない。


### 課題11: projectのパーサーを書こう

課題10を元に、projectのパーサーを書きます。

`project`から始まり、 `(`が続き、カラム名のカンマ区切りが続き、`)`で終わります。

パーサーの名前はpProjectとします。

### 課題12: filterのパーサーを書こう

同様にフィルタを書きます。パーサーの名前はpFilterにします。

引数は将来的にはandやorも対応しますが、まずは条件は一つにします。
つまり、いつも `[カラム名] = "文字列"` の形で、カンマとかも無しで一つだけです。

### 課題13: 両者のパースのどちらかを行う、pExpressionを作ろう

さて、課題12まででfilterとprojectのパーサーが出来ました。
次にこのfilterかprojectのどちらかをパースするパーサーを作りましょう。

filterとprojectのどちらか、という事を表す言葉は何が適切かは難しい所ですね。
ステートメントかExpressionとかがプログラムの用語ではよく使われます。
LEAPではexpressionと呼んでいるので、ここでもExpressionとしておきましょう。

という事でExpressionをパースするパーサー、pExpressionを作りましょう。

### 課題14: projectのパース結果を返す型をつくり、それを返そう

（なお、この課題の前に、試しに課題16を解いてみると、この課題の理解が進みます。この課題の意味がわからない場合は先に課題16を解いてみましょう）

ここまで書いたものをそのまま返していると、以下を実行した時に

```
let res = run pExpression "project([場所], [学年])"
```

単に文字列のリストとか文字のタプルなどが返ってきていて、
それがprojectなのかfilterなのかなどがわかりにくくなっていると思います。

「いやいや、リストはprojectでタプルがfilterだよ」とか言ってはいけません。
そこで、両者の型を別々に作り、それを返すようにしましょう。
手始めにpProjectだけやってみます。

なお、すでにやっている人はこの課題とこの次の課題はスキップしてOKです。

最終的に目指すのは、以下のような型を定義し、

```
type ProjectExpression =
  // なにか埋める

type FilterExpression =
  // なにか埋める

type Expression =
| ProjectExpression
| FilterExpression
```

pExpressionが `string->ParseResult<Expression, unit>` となるようにします。
突然ここから始めるのは大変かもしれないので、まずはprojectだけ始めます。

まずProjectExpressionの型を考えます。レコード型で、この処理を行う（deedleとつなげる事をイメージ）時に必要になる物を考えます。
課題8のproject関数の引数を見て、それに必要な情報が含まれるような型にすると良いでしょう。

で、次にpProjectProjecExpressionを「返す」ようにパーサーを変更します。

返す方法としては、チュートリアルの`|>>`, `stringReturn`, `pipe2`などを参考にしてみてください。

で、pProjectからProjectExpressionを返すようにします。

### 課題15: filterも型を作って、pExpressionでExpressionを返すようにする

次にfilterでも同じ作業をして、最後にpExpressionからExpressionを返すようにしましょう。

### 課題16: 両者のパースをくっつけて課題5のPlayDeedleとくっつけよう

pExpressionを課題5で作ったfilterやprojectとつなげてみましょう。
このくらいならまだScratch.fsxに全部書いてしまって良いと思います。

課題5の段階では引数に型が無かったと思いますが、課題15で作ったProjectExpressionやFilterExpressionを引数にとるように変更してつなげましょう。

以下が動く感じのrunExprを作ります。結果はDeedleのFrameを返すでいいでしょう。

```
runExpr "project([場所], [学年])" df

runExpr "filter([専門] = \"数学\")" df
```

### 基本的な言語処理系の構造を考える

パーサーには処理側は依存してないとかそういう話をここに書く。

## 第一回の終わりに

これで第一回は終わりです。
ライブラリを少しつついてチュートリアルやサンプルを見て使い方を覚える、という流れには慣れてきたでしょうか。

次の第二回から本格的にプログラムを開始していきます。


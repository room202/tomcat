# VS CodeでJSP/Servletを動かそう

## はじめに

この手順書どおりに進めると、自分のパソコンでJavaのWebページ（JSPとServlet）が表示できるようになります。かかる時間は30分くらいです。

**用意するもの**

- Windows 11のパソコン
- インターネット接続
- VS Code（インストール済みであること）

**用語メモ**

| 用語 | ひとことで言うと |
| --- | --- |
| JDK | Javaのプログラムを動かすための道具セット |
| Tomcat（トムキャット） | JSPやServletを動かす「Webサーバー」 |
| JSP | HTMLの中にJavaを書けるファイル |
| Servlet（サーブレット） | Webページを返すJavaのプログラム |

**注意**：フォルダ名やファイル名は、大文字・小文字も含めて手順書と同じにしてください。1文字でも違うと動きません。

## ステップ1：JDK（Java）をインストールする

Javaを動かすための道具を入れます。

1. ブラウザで「Eclipse Temurin」と検索し、公式サイト（adoptium.net）を開きます。
2. バージョン「21」（LTS）、OS「Windows」、種類「.msi」のファイルをダウンロードします。
3. ダウンロードしたファイルをダブルクリックして、インストールを始めます。
4. 途中の「カスタム セットアップ」画面で、**「Set JAVA\_HOME variable」** の左にある ✕ マークをクリックし、「ローカル ハード ドライブにインストール」を選びます。
5. あとは「次へ」→「インストール」で完了です。

**確認しよう**：スタートメニューで「cmd」と入力してコマンドプロンプトを開き、`java -version` と入力してEnterを押します。「21」という数字が表示されれば成功です。

## ステップ2：Tomcatをダウンロードして置く

WebサーバーのTomcatを用意します。インストールは不要で、置くだけで使えます。

1. ブラウザで「tomcat.apache.org」を開き、左のメニューの「Download」から **「Tomcat 11」** を選びます。
2. 「Core」の中にある **「64-bit Windows zip」** をクリックしてダウンロードします。
3. ダウンロードしたzipファイルを右クリック →「すべて展開」を選びます。
4. 展開してできたフォルダ（例：`apache-tomcat-11.0.xx`）の名前を **`tomcat`** に変更します。
5. その `tomcat` フォルダを **Cドライブの直下** に移動します。

**確認しよう**：`C:\tomcat` を開いたとき、中に `bin` や `webapps` というフォルダが見えればOKです。`C:\tomcat\apache-tomcat-11.0.xx\bin` のように1段深くなっていたら、置き方が間違っています。

## ステップ3：アプリ用フォルダを作る

Tomcatの `webapps` フォルダの中に、自分のアプリ用のフォルダを作ります。`webapps` の中のフォルダ1つが、アプリ1つになります。

1. エクスプローラーで `C:\tomcat\webapps` を開きます。
2. 右クリック →「新規作成」→「フォルダー」で、**`sample`** というフォルダを作ります。
3. `sample` の中に、次の3つのフォルダを作ります。
   - `src`
   - `WEB-INF`（すべて大文字）
   - `.vscode`（先頭に「.」が付きます）
4. `WEB-INF` の中に、**`classes`** というフォルダを作ります。

**できあがりの形**

```
C:\tomcat\webapps\sample
├─ .vscode
├─ src
└─ WEB-INF
    └─ classes
```

## ステップ4：VS Codeの拡張機能を入れてフォルダを開く

VS CodeでJavaとTomcatを使えるように、拡張機能を2つ追加します。

1. VS Codeを起動し、左側の四角が4つ並んだアイコン（拡張機能）をクリックします。キーボードの `Ctrl` + `Shift` + `X` でも開けます。
2. 検索欄に **`Extension Pack for Java`** と入力し、Microsoft製のものの「インストール」を押します。
3. 続けて **`Community Server Connectors`** と入力し、Red Hat製のものの「インストール」を押します。
4. 上のメニューの「ファイル」→「フォルダーを開く」を選び、`C:\tomcat\webapps\sample` を選んで開きます。
5. 「このフォルダー内のファイルの作成者を信頼しますか？」と聞かれたら、「はい、作成者を信頼します」を押します。

## ステップ5：設定ファイルを作る

VS Codeに「Javaのファイルはどこにあって、できあがったファイルをどこに置くか」を教える設定ファイルを作ります。

1. VS Codeの左側のファイル一覧で、`.vscode` フォルダを右クリック →「新しいファイル」を選びます。
2. ファイル名を **`settings.json`** にします。
3. 次の内容をそのままコピーして貼り付け、`Ctrl` + `S` で保存します。

```json
{
    "java.project.sourcePaths": ["src"],
    "java.project.outputPath": "WEB-INF/classes",
    "java.project.referencedLibraries": [
        "lib/**/*.jar",
        "c:\\tomcat\\lib\\servlet-api.jar"
    ]
}
```

**この設定の意味**

- 2行目：Javaのファイルは `src` フォルダに置く
- 3行目：できあがったファイル（.class）は `WEB-INF/classes` に置く
- 4〜7行目：使う部品（jarファイル）の場所。Servletを書くための部品はTomcatの中にあるものを使う

## ステップ6：JSPとServletを書く

動作確認用のJSPとServletを1つずつ作ります。

**① JSPを作る**

1. ファイル一覧の何もないところ（`sample` の直下）で右クリック →「新しいファイル」を選びます。
2. ファイル名を **`hello.jsp`** にして、次の内容を貼り付けて保存します。

```jsp
<%@ page contentType="text/html; charset=UTF-8" %>
<p>JSP動作中：<%= new java.util.Date() %></p>
```

**② Servletを作る**

1. `src` フォルダを右クリック →「新しいファイル」を選びます。
2. ファイル名を **`HelloServlet.java`** にして、次の内容を貼り付けて保存します。

```java
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;
import java.io.IOException;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse res)
            throws IOException {
        res.setContentType("text/html; charset=UTF-8");
        res.getWriter().println("Servlet動作中");
    }
}
```

**確認しよう**：保存してしばらく待つと、`WEB-INF\classes` の中に `HelloServlet.class` というファイルが自動でできます。これはVS Codeが自動で「コンパイル（Javaを機械が読める形に変換）」してくれたものです。

## ステップ7：Tomcatを起動する

VS CodeからTomcatを起動します。

1. VS Codeの左側のファイル一覧の一番下にある **「SERVERS」** をクリックして開きます。
2. 「Community Server Connector」が表示されるまで、数秒待ちます。
3. 「Community Server Connector」を右クリック →「Create New Server...」を選びます。
4. 「Download server?」と聞かれたら、**「No, use server on disk」** を選びます。
5. フォルダを選ぶ画面になるので、`C:\tomcat` を選びます。
6. 設定画面が出たら、そのまま「Finish」を押します。
7. SERVERSの中に追加された「tomcat-11.0.xx」を右クリック →**「Start Server」** を選びます。

**確認しよう**：サーバー名の横の表示が「Started」になれば起動成功です。Windowsのファイアウォールの許可を求める画面が出たら、「許可」を押してください。

## ステップ8：ブラウザで確認する

ブラウザのアドレス欄に次のURLを入力して、表示を確かめます。

| 入力するURL | 表示されれば成功 |
| --- | --- |
| `http://localhost:8080/sample/hello.jsp` | JSP動作中：（今の日時） |
| `http://localhost:8080/sample/hello` | Servlet動作中 |

URLの `localhost` は「自分のパソコン」、`8080` はTomcatの番号、`sample` はステップ3で作ったフォルダ名という意味です。2つとも表示できたら、環境づくりは完了です。

## うまくいかないとき

| こうなったら | まず試すこと |
| --- | --- |
| `java -version` で「認識されていません」と出る | ステップ1をやり直し、「Set JAVA\_HOME variable」を有効にする。終わったらコマンドプロンプトを開き直す |
| Servletの `import jakarta...` に赤い波線が出る | Ctrl+Shift+P →「Java: Clean Java Language Server Workspace」→「Restart and delete」を実行する。それでも出るなら `settings.json` のjarのパスを確認する。赤線があると .class も作られない |
| `HelloServlet.class` ができない | `HelloServlet.java` が `src` フォルダの中にあるか確認する |
| ブラウザに「404」と出る | URLのつづりと、フォルダ名 `sample`・`WEB-INF`（大文字）を確認する |
| ブラウザに「このサイトにアクセスできません」と出る | SERVERSでTomcatが「Started」になっているか確認する |
| 文字化けする | 1行目の `charset=UTF-8` が入っているか確認する |

**プログラムを直したとき**

- JSPを直したとき：保存してブラウザを更新するだけで反映されます。
- Servletを直したとき：保存したあと、SERVERSでTomcatを右クリック →「Restart Server」を選んでから、ブラウザを更新します。

blogチュートリアル(9) 記事の追加
================================

これまでのステップで、データベースに登録されているデータを表示できるようになりました！


次はいよいよ、フォームを作成してデータを追加・編集できるようにしてみましょう。


Postモデル用のフォームの編集
----------------------------

symfonyでモデルの生成コマンドを実行すると、モデルに対応するフォームクラス群も自動的に生成されています。
1つのモデルにつき、対応する1つのフォームクラスが生成されます。
フォームクラス群は、`lib/form/doctrine`ディレクトリに生成されています。

<pre>
sf_sandbox/
  lib/
    form/
      doctrine/
        PostForm.class.php
        base/
          BasePostForm.class.php
</pre>

これらのフォームクラスを使うことで、特定のモデルに対するCRUDを簡単に作成できるだけでなく、フォームのカスタマイズも行えるようになっています。

> **NOTE**
> モデルクラスと同じように、フォームクラスにも`～Form.class.php`と`Base～Form.class.php`の2つのクラスがあります。
> Base～クラスは基本設定が記述してあるクラスで、編集してはいけないことに注意して下さい。
> フォームクラスをカスタマイズする場合は、Baseのついていないファイルを編集します。



では、lib/form/doctrine/**PostForm.class.php**をエディタで開き、以下のように編集してください。

	[php]
	class PostForm extends BasePostForm
	{
	  public function configure()
	  {
	    $this->useFields(array('id', 'title', 'body'));
	  }
	}

生成されたフォームクラスには、デフォルトでデータベースのすべてのフィールドに対応するウィジェットが含まれています。
しかし、今回使用するのは**id**、**title**と**body**フィールドのみなので、useFieldsメソッドでこのフィールド以外を使わないよう設定しています。


> **NOTE**
> `PostForm`クラスにほとんどコードが書かれていないにもかかわらずウィジェットなどが表示されるのは、
> 継承元の`BasePostForm`クラスにウィジェットの定義が自動生成されているためです。



PostFormを使うnewアクションを作成
---------------------------------

次は、作成したPostFormを使って入力画面を処理するnewアクションを作成します。
`apps/frontend/modules/post/actions/actions.class.php`ファイルをエディタで開き、以下のコードをクラス内の末尾に**追加**してください。

	[php]
	public function executeNew(sfWebRequest $request)
	{
	  $this->form = new PostForm();
	  if ($request->isMethod(sfRequest::POST))
	  {
	    $this->form->bind($request->getParameter($this->form->getName()));
	    if ($this->form->isValid())
	    {
	      $this->form->save();
	      $this->getUser()->setFlash('info', 'データを保存しました。');
	      $this->redirect('post/index');
	    }
	  }
	}


このアクションでは、GETメソッドでアクセスした場合とPOSTメソッドでアクセスした場合の両方を扱っています。
最初にブラウザで「`post/new`」というURLにアクセスした場合はGETメソッド、
フォームに内容を入力して送信ボタンを押した場合はPOSTメソッドになります。
POSTメソッドの場合は、送信されたデータをフォームに**バインド**し、フォームに関連付けられているオブジェクト=Postモデルのレコードを保存しています。


> **NOTE**
> CakePHPのチュートリアルでは、フォームの初回表示と投稿時でのアクション内の分岐を、データの有無で判断しています。
> symfonyではREST(ful)の概念に基づき、このような判定をHTTPメソッドで行います。
> 具体的には、newアクションでの初回表示時はGETメソッドによるアクセス、フォームを送信した場合はPOSTメソッドによるアクセスとなっていることを利用します。


データの保存後は、flashメッセージを設定して`post/index`（一覧画面）へリダイレクトしています。
symfonyのflash機能では、単にメッセージを記録するのみです。（次のリダイレクト先まで有効）
リダイレクト後のビューにて、flashの文字列を取得して表示するようにしています。


> **NOTE**
> `actions.class.php`内に`require`や`include`を記述しなくても`PostForm`クラスを利用できるのは、
> symfonyによって`PostForm`クラスがオートロード対象に設定されるためです。
> symfonyのオートロード機能については、[symfonyのオートロード](14-next-step#2cca0e9ccc20e0b8ae938b179349df08)で説明しています。



フォームを表示するビューの作成
------------------------------

最後に、表示用のビューを作成します。
`apps/frontend/modules/post/templates/newSuccess.php`ファイルを作成し、以下のコードを入力して保存してください。

	[php]
	<?php echo $form->renderFormTag(url_for('post/new')) ?>
	<table>
	<?php echo $form ?>
	</table>
	<input type="submit" name="add" value="add" />
	</form>

まず、コードの先頭行ではフォームオブジェクトの`renderFormTag`メソッドを使ってformタグ（開始タグ）を生成しています。1番目の引数でフォームの送信先のアクションを指定しています（内部URI形式）。
`renderFormTag`メソッドを使うと、新規データの追加フォームの場合はPOSTメソッドになります。

3行目では、`$form`をechoしています。`$form`変数には`PostForm`のインスタンスが格納されています。
この変数をechoすると、フォームのウィジェットが描画されます。ウィジェットを描画する際に、デフォルトではテーブルタグの行として描画されますので、この出力部分をtableの開始タグと終了タグで囲んでいます。

> **CAUTION**
> symfonyには、フォームの終了タグをレンダリングするヘルパーはありません。
> ですので、フォームの終了タグは直接記述する必要があります。



動作の確認
----------

コードの追加が完了したら、ブラウザで「`http://localhost/sf_sandbox/web/frontend_dev.php/post/new`」にアクセスしてみてください。新規追加用のフォームが表示されたら、何かデータを入力して「add」ボタンをクリックし、データが正しく追加されるかどうか確認してください。

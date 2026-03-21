着手2026/3/8

入力値String sには"(", ")", "{", "}", "[", "]"のいずれかが含まれる。  
入力値が有効かを判定する。  
有効なのは、  
・かっこが同じ種類の閉じかっこで閉じられている。  
・かっこは正しい順序で閉じられている。  
・すべての閉じかっこには対応する同じ種類の開きかっこがある。  
  
#### 手作業で考える
①sの先頭からかっこを確認する。  
②最初に、開きかっこ`"(", "{", "["` が先頭に来ることを確認、メモ。そうでなければ   falseを返す。  
③次の文字を確認。  
④開きかっこメモがあるのに次の文字がなければ、ＮＧ。falseを返す。  
⑤開きかっこが来た場合、メモに残す。次の文字へ（③に戻る）  
⑥閉じかっこが来た場合、最後に書かれたメモを確認。  
⑦メモの末尾の開きかっこに対応していれば、OK。末尾のメモを消す。次の文字へ（③に戻る。）  
⑧メモの末尾の開きかっこに対応していなければNG。falseを返す。  
⑨メモが空ならＮＧ。falseを返す。  

#### STEP1
```java
class Solution {
	public boolean isValid(String s) {
		int length = s.length();
		
		if (s.charAt(0).equals(")")) {
			return false;
		} else if (s.charAt(0).equals("}")) {
			return false;
		} else if (s.charAt(0).equals("]")) {
			return false;
		}
		ArrayList<String> open = new ArrayList<>();
		for (int i = 0; i < length; i++) {
			if (s.charAt(i).contains(({[]}))) { //開きかっこ
				open.add(s.charAt(i));
			} else if (s.charAt(i).contains(({[]}))) { //閉じかっこ
				if (open.()) { //openリストの最後の要素が対応する開きかっこなら
					open.retain();
					continue;
				} else if () {//対応してない開きかっこなら
					return false;
				} else if () {//openリストが空なら
					return false;
				}
			}　else if () {//次の文字が空
				if (open) {//openリストが空じゃない
					return false;
				} else {
					return true;
				}
			}
		}
	}
}
```
わからない表現はいったんコメントに書いて大枠を作ってみたけど  
if のネストが多すぎてめちゃくちゃ嫌だ。  
  
claudeに確認させた。  
①[Deque (Java Platform SE 8 )](https://docs.oracle.com/javase/jp/8/docs/api/java/util/Deque.html)  
こういう型があるのか。恥ずかしいが、初めて見た。なるほど。  
charAt()はcharを返すので、equals()は使用不可。普通に`==`でよかった。  


#### 参考にしたほかの方の解答
[Create 20.md by hiroki-horiguchi-dev · Pull Request #6 · hiroki-horiguchi-dev/leetcode](https://github.com/hiroki-horiguchi-dev/leetcode/pull/6)  
[Create ValidParentheses.md by kt-from-j · Pull Request #6 · kt-from-j/leetcode](https://github.com/kt-from-j/leetcode/pull/6)   
[20. valid parentheses by HitoshiKoba · Pull Request #2 · HitoshiKoba/Arai60-public](https://github.com/HitoshiKoba/Arai60-public/pull/2)  

##### horiguchi-hiroki-devさんの解答
```java
class Solution {
	private static final Map<Character, Character> validParentheses = Map.of(
	'(', ')',
	'[', ']',
	'{', '}'
	)//Map.of()ってどんなメソッド？
	
	public boolean isValid(String s) {
		Deque<Character> openSymbols = new ArrayDeque<>();
		for (int i = 0; i < s.length(); i++) {
			char symbol = s.charAt(i);
			
			if (isIllegalSymbol(symbol)) {
				throw new IllegalArgumnetException("Invalid Character is detected, symbol is " + symbol);
			}
			
			if (isOpenSymbol(symbol)) {
				openSymbols.push(symbol);
			} else {
				if (openSymbols.isEmpty()) {
					return false;
				}
				char peekedOpenSymblos = openSymbols.peek();//peekとは？
				if (isMatched(symbol, peekedOpenSymbol)) {
					openSymbols.pop();} else {
					return false;
				}
			}
		}
		return openSymbols.isEmpty();//この行は何をしている？
	}
	
	private boolean isIllegalSymbol(char symbol) {
		return !validParentheses.containsKey(symbol) && !validParentheses.containsValue(symbol);
	}
	private boolean isOpenSymbol(char symbol) {
		return validParetntheses.containsKey(symbol);
	}
	
	private boolean isMatched(char symbol, char peekedOpenSymbol) {
		return validParentheses.get(peekedOpenSymbol) == symbol;
	}
}
```
①Map.of()メソッド  
[Map (Java SE 11 & JDK 11 )](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/util/Map.html)  
>これらは[_変更不可_](https://docs.oracle.com/javase/jp/11/docs/api/java.base/java/util/Collection.html#unmodifiable)です。 キーおよび値は追加、削除または更新できません。  

変更不可でMapを作成する意味がよく理解できなかった。  
かっこの対応表という意味では、変更不可のmapにする必要性はあまりないかと思った。  
claudeに聞いた結果→読み手に対して「このmapは変更されることがない」と明示する意味があるとのこと。  
下記④を考えてみたら、外部から万が一対応表の中身を変更されると正しく動かなくなるから、  
変更不可にする意味はやはりあると思い直した。  
  
②ArrayDequeの基本操作  
push()：末尾に追加する。  
peek()：Arraydequeの末尾（head）を返す。削除はしない。  
headは、最後にpushされた要素のこと。少し混乱しそう。前回解いたLinkedListではheadは文字通り先頭の要素を指していた。  
pop()：末尾を取り出す。peekの削除あり。  

③return openSymbols.empty();について  
empty()メソッドはStackに存在する、  
DequeではisEmpty()メソッドとのこと。  
要素が存在しない場合、trueを返す。  
文字列をすべて走査し終わり、開きかっこのDequeに要素が残っていなければtrueを返している。逆に、文字列をすべて走査したのに、開きかっこが組にならず残っていたらfalseを返す。  
  
④validParenthesesのprivate static finalにする意図は？  
- claudeと壁打ち  
 - private→このクラスの内部でしか触れないようにする。
 - static→isValidが呼び出されるごとにmapが生成されるのを防ぐ
 - final→validParenthesesの中身を書き換えられないようにする
 - static finalは定数を宣言するときに使うみたい。今回のかっこの対応表も中身は固定で変わらないので定数みたいなものなのかな。外部から勝手に中身を書き換えられても、正しく動かなくなるから、変更できないようにしているっぽい。
  
##### kt-from-jさんのコード
```java
import java.util.Objects;

class Solution {
	public boolean isValid(String s) {
		Deque<Character> openBracketsStack = new ArrayDeque<>();
		Map<Character, Character> openToClose = new HashMap<>();
		openToClose.put('(', ')');
		openToClose.put('[', ']');
		openToClose.put('{', '}');
		
		for (Character bracket : s.toCharArray()) {
			if (openToClose.containsKey(bracket)) {
				openBracketsStack.push(bracket);
				continue;
			}
			if (openBracketStack.isEmpty()) {
				return false;
			}
			Character openBracket = openBracketsStack.pop();
			Character closeBracket = openToClose.get(openBracket);
			if (!Objects.equals(bracket, closeBracket)) {
				return false;
			}
		}
		return openBracketsStack.isEmpty();
	}
}
```
 - Objects.equals(bracket, closeBracket)
  - この書き方できるのか？Objectsはどこからでてきたのかな
   - java.util.Objects
  - .equals()だと、nullが渡されたとき、nullPointerExceptionが発生するが、Objects.equals()だとnullセーフ
  - [jdk7u-jdk/src/share/classes/java/util/Objects.java at master · openjdk-mirror/jdk7u-jdk](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/util/Objects.java)
　```java
　    public static boolean equals(Object a, Object b) {
        return (a == b) || (a != null && a.equals(b));
    }
　```

#### STEP3
- 例外処理や関数の書き方の練習も兼ねて、horiguchi-hiroki-devさんの解答を写経することにする。
 - 1回目その1
  - 複数形で宣言したvalidParenthesesを45行目で単数形で書いてエラー
  - 30行目のreturn false;を書き忘れて、正しい結果が返らない。
  - 10分ギリギリ
- 1回目その2
- 普通にタイピングしていると10分ギリギリなので、コピぺを活用することにした。
 - Map.ofの最後に,をつけてしまいエラー（コピペによるミス）
 - openSymbolsのタイポ
 - containsValueのタイポ
 - コピペしてきた!validParenthesesの!消し忘れ（コピペによるミス）
 - コピペすると予想外のところでエラーとなってしまう。普通に書いた方がいいか。。
 - それか、絶対に同じになる部分だけコピペする。それでも間違えそうな気がする。
 - 今回はコードの量が多いという要因があり、時間内に収めることは必ずしも本質的ではないと考えたので、タイピング速度によって10分を多少超えてしまうのは許容することにする。
- 1回目その3
 - 18行目、if (isOpenSymbol) {}と引数を書くのを忘れてエラー
 - containsKeyのタイポ
- 1回目その4
 - isEmpty()に間違って引数symbolを書いてしまいエラー
- 1回目その5
 - OK
- 2回目
 - OK
- 3回目
 - OK

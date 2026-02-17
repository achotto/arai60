
#### STEP1
問題の意味はわかるけど、いろいろなことが全然わからない。  
LinkedList自体、聞いたことがある程度。  
よく見たら、Inputのデータ型がListNodeとなっている。  
```java
public class Solution {

    public boolean hasCycle(ListNode head) {

    }

}
```
そんな型初めて見た  
Listnodeという型はJDKに用意されていない。  
→問題用に用意されたクラスで、コメントにListNodeのクラスが記述されている。  
[What type of datatype is ListNode in java? - Stack Overflow](https://stackoverflow.com/questions/76708470/what-type-of-datatype-is-listnode-in-java)  
LinkedListとしてはjavaに存在する。  
  
問題文にあるposがどういう意図のものなのかよくわからなかった。  
  
#### 参考
[LinkedList (Java Platform SE 8 )](https://docs.oracle.com/javase/jp/8/docs/api/java/util/LinkedList.html)  
[Javaでリスト構造 – 電子情報工学科](https://www.ei.fukui-nct.ac.jp/2025/06/09/list-java-2025/)  
LinkedList（一方向）は、自分のノードに次ノードへの参照を持っているデータ構造  
#### 手作業で考える
①今いるノードを、「通ったことがあるメモ」に記録する  
②次ノードをたどる  
③次ノードにきたら、メモを確認し、記録があれば、Cycleがあると判断する  
④なければ、「通ったことがあるメモ」に記録する  （①～④を繰り返す）  
  
#### 参照したほかの方のプルリクエスト(java)・コメント集
https://github.com/appseed246/arai60/pull/2
[Create LinkedListCycle.md by kt-from-j · Pull Request #1 · kt-from-j/leetcode](https://github.com/kt-from-j/leetcode/pull/1)  
[141.LinkedList Cycle (hashSetで実装) by mizuresort · Pull Request #1 · mizuresort/LeetCode](https://github.com/mizuresort/LeetCode/pull/1)  
[141. Linked List Cycle by tk-hirom · Pull Request #1 · tk-hirom/Arai60](https://github.com/tk-hirom/Arai60/pull/1#discussion_r1641231416)  
[コーディング練習会典型コメント集(一般社団法人ソフトウェアエンジニアリング協会) - Google ドキュメント](https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.2k4z0wt6ytf9)  
「フロイドの循環検出法」という方法でもサイクルがあるか見つけられるとのこと。  
速度が速いポインタと遅いポインタを回して、サイクルがあればいつか2つのポインタが同じ位置に来る。  
ただ、本来この問題で見たいポイントではないみたいなので、トリビア的に知っておくくらいにしておく。  


##### appseed246さんのSTEP4  
```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
	public boolean hasCycle(ListNode head) {
		Set<ListNode> visited = new HashSet<ListNode>();
		ListNode node = head;
		
		while (node != null) {
			if (visited.contains(node)) {
				return true;
			} 
			visited.add(node);
			node = node.next;
		}
		
		return false;
	}
}
```
  
##### kt-from-jさんのSTEP3  
```java
public class Solution {
	public boolean hasCycle (ListNode head) {
	if (head == null) return false;
	HashSet<ListNode> visited = new HashSet<>();
	ListNode node = head;
	while (node != null && node.next != null) 
		if (visited.contains(node)) return true;
		visited.add(node);
		node = node.next;
	}
}

```
①6行目のwhile条件の2つ目 「node.next のチェックっていります? いや、してはだめということはないですが。」というレビューコメントがあった。  
自分のノードと次ノードが両方存在すれば、循環のチェックをする  
自分のノードがなければ、次ノードもない  
自分のノードがあれば、次ノードはある／ない両方あり得る  
自分のノードがあり、次ノードがない場合、サイクルはない  
自分のノードがあり、次ノードがある場合、サイクルの可能性がある。  
次ノードがあるときに循環のチェックはどちらにせよするので  
条件にする理由はない？  
ということだろうか  
②if () return true/false;という書き方ができるのか  
if () {return true/false;}しかダメだと思っていた。  
ただし、「ぶら下がりif文」（というらしい）は、  
バグの原因になる旨レビューコメントにあり。  

{}なしのif文の中に別のif文がくっついていて、  
elseがどのifに対応しているのか読み取りにくくなることらしい。  

if の中身が1行しかない場合は、if () ～;ができるみたい。知らなかった。  
知識として、知っている必要はあると思う。  
自分で書く時は普通に{}で書いた方がよさそう。  

##### mizuresortさんのSTEP3  
```java
/**
Difinition for singly-linked list.
class ListNode {
	int val;
	ListNode next;
	ListNode(int x) {
		val = x;
		next = null;
	}
}
**/
import java.util.Set;
import java.util.HashSet;
public class Solution {
	public boolean hasCycle (ListNode head) {
		Set<ListNode> visitedNode = new HashSet<>();
		ListNode node = head;
		
		while (node != null) {
			if (visitedNode.contains(node)) {
				return true;
			}
			visitedNode.add(node);
			node = node.next;
		}
		return false;
	}
}
```
①importをちゃんと書いてある。  
そもそも自分でimportを書いたことがなくあまりわからない。  
[Javaで他のクラスを使う「import文」とは？](https://sitc.ac/yogo/java_012.html)  
  
②Setの書き方に二種類ある。この違いは？  
```java
Set<ListNode> visited = new HashSet<>();
Set<ListNode> visited = new HashSet<ListNode>();
```
java7以降の標準的な書き方が一番目の書き方とのこと。

③ListNode node = head;しているけど、別の変数を新しく作る理由は？  
原本のheadではなく、作業用で作業するためだと思うんだけど、  
今回のコードでは、原本を破壊や変更するわけではないので  
いらないとも思う。  
chatGPTに聞いてみたところ、以下の回答だった。  
なぜ head をそのまま使わないのか  
理由①：API の契約（副作用を出さない）  
head は：  
「呼び出し元から渡された入口」  
メソッドの 入力引数  
慣例として：  
引数は 不変として扱う  
走査用の変数を別に作る  
これは Java 仕様ではなく 設計上の約束事。  
理由②：意味の分離（役割が違う）  
head  
リストの先頭という 意味を持つ名前  
node  
「今見ているノード」という 走査状態  
役割が違う。  
理由③：将来の変更耐性  
今は：  
node = node.next;  
だけだが、  
デバッグ  
ログ  
head を後で再利用  
別の走査を追加  
こうした変更で head を失わない。  


#### STEP3  
importが書いてあり実務に近いと思い、mizuresortさんのコードを写経して
3回連続でエラーなく通す。
1回目
import から書き、java.utilをjava.utilsと書いてしまってエラー  
1回目その２  
OK  
2回目  
OK  
3回目  
OK  

Given the `head` of a sorted linked list, _delete all duplicates such that each element appears only once_. Return _the linked list **sorted** as well_.  
ソート済みリンクリストから、重複を取り除き、  
重複削除後のソート済みリンクリストを返す。  
[Remove Duplicates from Sorted List - LeetCode](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)  
  
#### 手作業で考えてみる
①ノードを進む。valueを記録。  
②次ノードを見て、valueが同じなら、次の次のノードを見る。  
valueが違うノードが見つかったら、nextをそこへつなぐ。  
③つないだノードへ進む。  
同じことを繰り返す  
  
※うまく動かないが自分で書こうとしてみたものを一応載せておく  
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
		ListNode currentNode = head;
		ListNode followingNode = head.next;
		
		while (currentNode.next != null) {
			if (followingNode == null) {
				currentNode.next = null;
			}
			if (currentNode.val != followingNode.val) {
				if (currentNode.next != followingNode) {
					currentNode.next = followingNode;
				}
				currentNode = followingNode;
				continue;
			}
			followingNode = followingNode.next;
		}
		return head;
    }
}
```
Claudeに上記のコードを投げた。  
・followingNode == nullのとき、そのまま9行目に進むのでぬるぽが発生すると指摘。  
・条件が整理できてない。→頭の中だけで整理するのは限界がある。  
・付け替えの処理がうまくできてない。→理解ができてない。  
・2ポインタで管理しようとして、複雑になり頭で整理しきれなくなっているとのこと。  
  
・STEP2でhiroki-horigushi-devさんのコードを読み、followinNodeとcurrentNodeは変数名が長すぎるので、current、followingでもいいと思った。  
  
#### 参考にしたほかの方の解答（java）  
[83. Remove Duplicates from Sorted List by appseed246 · Pull Request #4 · appseed246/arai60](https://github.com/appseed246/arai60/pull/4)  
[Create 83.md by hiroki-horiguchi-dev · Pull Request #3 · hiroki-horiguchi-dev/leetcode](https://github.com/hiroki-horiguchi-dev/leetcode/pull/3)  
[Create RemoveDuplicatesfromSortedList.md by kt-from-j · Pull Request #3 · kt-from-j/leetcode](https://github.com/kt-from-j/leetcode/pull/3)  



##### appseed246さんのコード
```java
class Solution {
	public ListNode deleteDuplicates(ListNode head) {
		ListNode node = head;
		
		while (node != null && node.next != null) {
			if (node.val == node.next.val) {
				node.next = node.next.next;
			} else {
				node = node.next;
			}
		}
		
		return head;
	}
}
```
ループ・条件がシンプルにまとまっている。 
これは1重ループでやる方法。  
  
hiroki-horigushi-devさんのコード  
```java
class Solution {
	public ListNode deleteDuplicates(ListNode head) {
		//レビュワーの仕様理解負担軽減を目的としてあえて書いておく
		if (head == null) {
			return null;
		}
		
		ListNode current = head;
		while (current != null && current.next != null) {
			ListNode forward = current.next;
			//重複しなくなるまでforward1を飛ばす
			while (forward != null && current.val == forward.val) {
				forward = forward.next;
			}
			current.next = forward;
			current = forward;
		}
		return head;
	}
}

```
変数名がcurrentとforwardとなっている所が、  
今何を操作しようとしているかがわかりやすいと感じた。  
こちらは2重ループ  
  
kt-from-jさんのコード  
```java
class Solution {
	public ListNode deleteDuplicates(ListNode head) {
		if (head == null) {
			return null;
		}
		
		ListNode node = head;
		while (node != null && node.next != null) {
			if (node.val == node.next.val) {
				node.next = node.next.next;
				continue;
			}
			node = node.next;
		}
		return head;
	}
}
```
1重ループ。  
最初にhead==nullの時のガード節がある。個人的には、明示的にガードしてある方が読みやすいと思ったが  
一方で、このガード節があるなら、（私のトレースの誤りがなければ）そのあとループの中でnodeがnullになることがなく、  
while (node != null && node.next != null)のnode != nullの条件は不要ではないかと思った。  
  
ほかの方の読んでみての感想  
コードを読めば理解はできるけど、本当にそのコードですべてのケースで正しく動くのかが判断できない。  
ワーキングメモリがあふれてしまうのが原因かもしれない。  
地道にトレースしながら理解していくしかないか。  
  
#### STEP3  
ほかの方の解答を使って、3回連続でacceptするまで書く。  
2重ループはまだ頭の中でついていけないので  
今回は1重ループで書いているappseed246さんのコードを使わせていただく。  
  
1回目その１  
・node.valをnode.valueと書いてしまい、エラー  
ListNodeクラス内でvalueではなく、valとなっているのでNG。  
1回目その２  
・その１と同じエラー。注意散漫  
1回目その３  
・OK  
2回目  
・1回目と同じエラー  
1回目その４  
・OK  
2回目その２  
・OK  
3回目  
・OK  

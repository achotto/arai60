[206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)  

LinkedListのheadが与えられるので、  
リストを逆順にして、riversed listを返す。  
例１  
**Input:** head = [1,2,3,4,5]  
**Output:** [5,4,3,2,1]  
例２  
**Input:** head = [1,2]  
**Output:** [2,1]    
例３  
**Input:** head = []  
**Output:** []  

#### STEP1
- 手作業で考える
 1. 自ノードが空かつ次ノードが空になるまで、ノードの先頭からスタックに積んでいく
 2. スタックが空になるまでpopを繰り返すことで逆順のノードを構築する。

```java
class Solution{
	public ListNode reverseList(ListNode head) {
		Deque<ListNode> nodeStack = new ArrayDeque<>();
		ListNode node = head;
		
		//ノードが空なら、何もせずノードを返す。
		if (node == null) {
			return node;
		} 
		
		//ノードをスタックに積んでいく
		//while (node != null && node.next != null) { //修正
		while(node != null) { //修正
			nodeStack.push(node);
			node = node.next;
		}
		//積んだノードを上から取り出して、nextノードの付け替えを行う
		//ListNode reversedNode = nodeStack.pop();　//修正
		ListNode reversedHead = nodeStack.pop(); //修正
		ListNode reversedNode = reversedHead; //修正
		while (!nodeStack.isEmpty()) {
			reversedNode.next = nodeStack.pop();
			reversedNode = reversedNode.next;
			if (nodeStack.isEmpty()) {
				reversedNode.next = null;
				break;
			}
		}

    //return reversedNode; //修正
		return reversedHead;
	}
}
```
1. pop()とremove()の違い→どちらも先頭をremoveして返す。調べたら、DequeがStackとQueueの両方として使えるので、メソッドも２系統用意されている。stackとして使う意図を伝達するために、popを使うのが望ましい。  
2. エラーは出なかったが、正しい値を返さなかった。
    input[1, 2, 3, 4, 5]
    output[1]
3. 最初のループの条件が間違っている。最後のノードはnext = nullになるので、
	最後だけがstackに積まれないでループ終わっちゃうな。
4. 正しい値を返さない原因→revesedNodeを返しているから。最後にくっつけた1が返る。
	reversedNodeとは別に、reversedHeadを用意した。
8. claudeに壁打ちし、今回は計算量も考えた。
	空間計算量は、ノードの数の分だけなので、O(N)
	時間計算量は、O(2N)（ループ2回）と考えたが、定数倍を無視するのでO(N)


#### STEP2
参考にしたほかの方のコードなど  (java)
[206. Reverse Linked List by katsukii · Pull Request #22 · katsukii/leetcode](https://github.com/katsukii/leetcode/pull/22)  
[206. Reverse Linked List by ryoooooory · Pull Request #14 · ryoooooory/LeetCode](https://github.com/ryoooooory/LeetCode/pull/14)  
[206/reverse linked list by jjysogfy · Pull Request #2 · jjysogfy/arai60-202603](https://github.com/jjysogfy/arai60-202603/pull/2)  
- Arai60でstackのカテゴリに入っていたので、「stackだ！」と思って解いたけど、あまりそこには依存していないみたい。
1. 再帰で解く方法
2. 「リンク」を中心に見ていて範囲内のすべてのリンクを順番にひっくり返すという方法
3. 先頭の前にダミーをつけて、先頭の次のノードをダミーの後ろに挿入していく方法  
（端っこの目印になるダミーのことを番兵 sentinelというらしい。）  
4. ひっくりかえす前の鎖と後の鎖を用意して、前のやつの先頭を後のやつの先頭につけていく方法
- いろいろなやり方ができるとなると、この問題の本質はなんだろう？

1. 再帰で解く 
```java
public ListNode reverseList(ListNode current, ListNode previous) {
    if (current == null) return previous;
    ListNode newHead = reverseList(current.next, current);
    current.next = previous;
    return newHead;
}
```
- かなりトリッキー。トレースしたら確かに動くことが分かったけど、どうやって自分で考えられるんだろう。  
[206. Reverse Linked List by syoshida20 · Pull Request #12 · syoshida20/leetcode]  
(https://github.com/syoshida20/leetcode/pull/12/changes#diff-62f050819b0cae018db7450bb0f0341942d799ece31ce3d0d1d4a64d45578363R24)  
※こちらのjavascriptのコードをclaudeに渡してjavaに変えてもらったもの。
- クロード曰く、再帰とは暗黙のスタックである（スタックトレース）つまり、スタックを用いた解法とやっていることは同じ

2.  「リンク」を中心に見ていて範囲内のすべてのリンクを順番にひっくり返すという方法
```java
class Solution {
	public ListNode reverseList(ListNode head) {
		if (head == null || head.next == null) {
			return head;
		}
		
		ListNode lastSeen = null;
		ListNode node = head;
		
		while (node != null) {
			ListNode oldNext = node.next;
			node.next = lastSeen;
			lastSeen = node;
			node = oldNext;
		}
		
		return lastSeen;
	}
}
```
- 何をループで受け渡ししているか？
 - 逆向きに付け替え済みのノード（lastSeen）と、まだ付け替えてないノードの先頭（node）
 - つまり、イテレーション間で、「ここまでは付け替えの処理完了しました」・「あなたの担当はこのノードです」を渡している。
 - 「まだ付け替えていない分」を意味しているnodeがnullになったらwhileが終了する。
 - while終了後、「付け替え処理が完了した分」であるlastSeenをreturnすることで、逆順のノードを返すことができる。
 

3. 先頭の前にダミーをつけて、先頭の次のノードをダミーの後ろに挿入していく方法
```java
class Solution {
	public ListNode reverseList(ListNode head) {
		if (head == null || head.next == null) {
			return head;
		}
		
		ListNode dummyHead = new ListNode(0, null);
		ListNode node = head;
		
		while (node != null) {
			ListNode oldNext = node.next;
			node.next  = dummyHead.next;
			dummyHead.next = node;
			node = oldNext;
		}
		return dummyHead.next;
	}
}
```
- ループで受け渡しているもの
 - 逆向きに付け替え済みのノード（dummyHead.next）とまだ付け替えてないノードの先頭(node)

#### STEP3
リンクリスト操作の練習として1回の走査でノードの付け替えをしながら進む方法で練習する。
- 1回目 return nodeしてしまいテストケースを通らない。最後のループでnodeにnullが来るからnodeを返してはだめだった。
- 2回目 OK
- 3回目 OK
- 4回目 OK
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
class Solution{
	public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode node = head;
        ListNode lastSeen = null;

        while (node != null) {
            ListNode oldNext = node.next;
            node.next =  lastSeen;
            lastSeen = node;
            node = oldNext;
        }

        return lastSeen;
	}
}
```

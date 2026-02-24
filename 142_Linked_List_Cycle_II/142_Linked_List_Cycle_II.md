#### STEP1
##### 手作業を考える
①ノードを「通ったことがあるノード」メモに記録する  
②次ノードに行く  
③「通ったことがあるノード」メモに、今いるノードが載っていてれば、今のノードを返す  
なければ、メモに記録して次に行く  
④次ノードがないノード（LinkedListの最後）に来たらnullを返す  
141とほぼ同じことをしているけど、これでいいのか？    


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
import java.util.Set;
import java.util.HashSet;

public class Solution {
    public ListNode detectCycle(ListNode head) {
        Set<ListNode> visitedNode = new HashSet<>();
        ListNode node = head;
        while (node != null) {
            if (visitedNode.contains(node)) {
                return node;
            }
            visitedNode.add(node);
            node = node.next;
        }
        return null;
    }
}
```

#### STEP2
##### 参考にしたほかの方の解答(java)
https://github.com/appseed246/arai60/pull/3  
https://github.com/katsukii/leetcode/pull/13  
https://github.com/kt-from-j/leetcode/pull/2  
[コーディング練習会典型コメント集(一般社団法人ソフトウェアエンジニアリング協会) - Google ドキュメント](https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.jfs03xpyyrfl)  

[142. Linked List Cycle II by pineappleYogurt · Pull Request #3 · pineappleYogurt/leetCode](https://github.com/pineappleYogurt/leetCode/pull/3)    
※これはjavaじゃない  

Setを使って書く方法だと、141の問題とほぼ同じコードになり、あまり142に取り組む意味がなくなってしまうので、  
ほかの人を真似して、この問題をフロイドの循環検出法で書いてみる。  
フロイドの循環検出法だと、うさぎとかめの「衝突点」は検出できるけど、  
「サイクルの始点」を検出する方法を考えないといけないことに気づいた。  
> はい。2歩ずつ走るうさぎと1歩ずつ歩くかめが、ある地点でぶつかったとします。そこを衝突点と呼びましょう。衝突点が見つかったあとに、衝突点とスタート地点から1歩ずつうさぎとかめを歩かせて、衝突するところが、合流地点である、ということを理解したいということですね。

> うさぎとかめは、衝突点で出会った後に、うさぎとかめは、いま来た道を戻るように言われました。うさぎもかめも同じ速さで1歩ずつ歩いて戻ります。このとき、うさぎは一周してから戻りますが、かめはそのまま戻ります。

> かめがスタート地点に戻った時、うさぎはどこにいるでしょうか。実は、うさぎは衝突点にいます。なぜかというと、うさぎは倍速で走っているからです。スタート地点から衝突点を通って衝突点に到達するうさぎルートの長さは、スタートから衝突点に到達するかめルートの2倍だからです。

> ところで、この戻っていく時、うさぎとかめは、衝突点から同じ速さで歩いて戻っているので、合流点までは一緒にいましたね。

> さて、ここまでの話を動画にして逆回しにしてみましょう。

> うさぎとかめは、それぞれ衝突点とスタート地点から同じ速さで後ろ向きに歩き始めます。そして、合流地点から一緒に後ろ向きに歩き始め、そして衝突点に到達します。

紙に書いて整理してみたら、確かにそうなるけど、腹落ちするところまではいけていない。  
うさぎはかめの2倍の速さで進んでいるので、かめがheadから衝突点まで進むとき、  
うさぎはかめの2倍の距離を通って衝突点に到達している  
head～サイクル始点までの距離 a  
サイクル始点～衝突点までの距離 b  
サイクル長 c  
かめが衝突点に来るとき、うさぎはサイクルを何周かしている  
a + b = nc  
a = nc - b  
head～サイクル始点までの距離 aは、nc - bと等しい？  
うーん、まだ理解ができてない。いったん宿題とする。。  

##### appseed246さんのSTEP2
```java
public class Solution {
	public ListNode detectCycle(ListNode head) {
	
		ListNode collisionPoint = findCollisionPoint(head);
		
		if (collisionPoint == null) {
			return null;
		}
		
		ListNode fromStart = head;
		ListNode fromCollision = collisionPoint;
		
		while (fromStart != fromCollision) {
			fromStart = fromStart.next;
			fromCollision = fromCollision.next;
		}
		
		return fromStart;
	}
	
	private ListNode findCollisionPoint(ListNode head) {
		ListNode fast = head;
		ListNode slow = head;
		
		while (fast != null && fast.next != null) {
			fast = fast.next.next;
			slow = slow.next;
			
			if (fast == slow) {
				reutrn fast;
			}
		}
		
		return null;
	}
}
```
レビューコメントでなるほどと思ったところ。  
>個人的には、関数化をする方が、脳のワーキングメモリを使わずに済むのと、  
>他のエンジニアに衝突店の検出のロジックを読み飛ばす選択肢も与えられるため  
>好みです。  


##### katsukiiさんのコード  
```java
public class Solution {
	public ListNode detectCycle(ListNode head) {
		ListNode meetingPoint = findMeetingtPoint(head);
		if (meetingPoint == null) return null;
		return findCycleStart(head, meetingPoint);
	}
	
	// Step 1: Detect if there is a cycle and return the meeting point
	private ListNode findMeetingPoint(LitsNode head) {
		ListNode fast = head;
		ListNode slow = head;
		
		while (fast != null && fast.next != null) {
			fast = fast.next.next;
			slow = slow.next;
			if (fast == slow) {
				return fast; // Meeting point
			}
		}
		return null; // No cycle
	} 
	
	// Step 2: Find the start of the cycle
	private ListNode findCycleStart(ListNode head, ListNode meetingPoint) {
		ListNode slow1 = head;
		ListNode slow2 = meetingPoint;
		
		while (slow != slow2) {
			slow1 = slow1.next;
			slow2 = slow2.next;
		}
		return slow1;
	}
}
```
##### kt-from-jさんのコード  
```java
public class Solution {
	public ListNode detectCycle(ListNode head) {
		if (head == null) {
			return null;
		}
		
		//ループを検出
		ListNode slowNode = head;
		ListNode fastNode = head;
		while (fastNode != null && fastNode.next != null) {
			slowNode = slowNode.next;
			fastNode = fastNode.next.next;
			//衝突を検知したら中断
			if (slowNode == fastNode) {
				break;
			}
		}
		
		if (fastNode == null || fastNode.next == null) {
			return null;
		}
		
		//ループの始点を探索
		//fastNodeを先頭に戻し、衝突するまで進める。
		//衝突した地点がループの始点
		fastNode = head;
		while (fastNode != slowNode) {
			slowNode = slowNode.next;
			fastNode = fastNode.next;
		}
		return fastNode;
	}
}
```
①サイクルの始まり検出時、うさぎ（fast）をheadにもどしているけど、かめじゃなかったっけ？  
戻すのはうさぎでもいいの？だとしたらその理屈は？  
→2つのポインタはひとつづつしか進まないから、戻すのはうさぎとかめどっちでもいい。  
サイクル始まりの検出時はどちらか片方がhead,もう片方が衝突点にいればいい。  
（サイクルの始まり検出時は、fastとslowは関係ない。  
変数名fast・slowをそのままにしているのは、  
かえって何か意味があるのかと考えてしまったので、  
自分的には別の変数のほうがよいかと思った。）  

②setを使った解法についていたレビューコメント  

```
HashSet<ListNode> visitedNodes = new HashSet<>();
```
>自分なら、HashSet の実装に依存しないコードであれば、
>```java
>Set<ListNode> visitedNodes = new HashSet<>();
>```
>のように、インターフェースで受け取るのですが、好みの問題かもしれません。

ここがよくわからなかったのでGPTに聞いてみた  
GPTの解説  
設計の原則：  
> 具体クラスではなく、抽象（インターフェース）に依存する  
これを  
**「プログラミングは実装ではなくインターフェースに対して行え」**
と言う。  
（SOLID原則の一部の考え方）  
- Set = 仕様  
- HashSet = 高速重視、順番なし  
- LinkedHashSet = 挿入順  
- TreeSet = ソート順  
  
原則  
- 基本はインターフェースで受ける  
例外  
- その具体クラスの追加機能を使う場合は具体型で受ける  
だからあなたの整理はこうなる  
× Set が欲しいなら実質 HashSet  
○ Set が欲しいなら、実装は今は HashSet を選ぶだけ  


#### STEP3
関数は自分でちゃんと書いたことがなく、  
衝突点の検出と、サイクル始点の検出の両方を関数化している  
katsukiiさんのコードでSTEP3に取り組む。  
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode meetingPoint = findMeetingPoint(head);
        if (meetingPoint == null) {
            return null;
        }
        return findCycleStart(head, meetingPoint);
    }
  
    private ListNode findMeetingPoint(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while (fast != null && fast.next!= null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                return fast;
            }
        }
        return null;
    }

    private ListNode findCycleStart(ListNode head, ListNode meetingPoint) {
        ListNode slow1 = head;
        ListNode slow2 = meetingPoint;
        while (slow1 != slow2) {
            slow1 = slow1.next;
            slow2 = slow2.next;
        }
        return slow1;
    }
}
```

１回目その１  
-returnのタイポ  
-meetingPointのタイポ  
１回目その２  
-OK  
２回目  
-OK  
３回目  
-OK  

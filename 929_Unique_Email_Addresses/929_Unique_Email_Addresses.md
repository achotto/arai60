##STEP1  
手作業でどうするか  
・アドレスの正規化を行う。  
・正規化するごとに一覧に追加する。  
・最後にセットの要素数を返す。  
  
```java
class Solution {
    public int numUniqueEmails(String[] emails) {
        Set<String> validAddresses = new HashSet<>();
        for (int i = 0; i < emails.length; i++) {
            if (!emails[i].contains("@")) {
                continue;
            }
            String localName = emails[i].replaceFirst("@.*$", "");
            String domainName = emails[i].replaceFirst("^.*(?=@)", "");
            localName = localName.replaceFirst("\\+.*$", "");
            localName = localName.replace(".", ""); 
            String addresses = localName + domainName;
            validAddresses.add(addresses);
        }
        return validAddresses.size();
    }
}
```
##メモ  
文字列の処理と聞いて、一番最初に正規表現を思いついた。  
正規表現と置換を使ったら書けそうだと思った。  
置換のメソッドを調べてSTEP1完了。  
javaのreplace系のメソッド  
replace(char oldChar, char newChar)  
replace(CharSequence target, CharSequence replacement)  
replaceAll(String regex, String replacement)  
正規表現にマッチする文字列を置換する。  
→replaceAllとあるので、すべての文字列を置き換えるのかと思っていたら、正規表現を使う置換はreplaceAll、  正規表現を使わない置換はreplace  
replaceFirst(String regex, String replacement)  
→正規表現を使った置換（最初にマッチする文字列のみ）  

正規表現を使わなくても解くことは可能そうだと思う。  
が、調べものやほかの方のコードを読んでまとめるのに1,2週間かかってしまうので、いったんは一つのやり方だけで進める。  
  
##STEP2  
splitとreplaceについて  
ローカルとドメインの分割処理をsplitを使っているやり方を見た。  
https://github.com/Yuto729/LeetCode_arai60/pull/19/changes/d966a8218194705abe6ad9000bdc07c3a3eb7d76  
javaのsplit  
https://docs.oracle.com/javase/jp/8/docs/api/java/lang/String.html#split-java.lang.String-  
単純に@を指定してsplitをすると、結果から@が消えてしまう。  
```java
String emails[i].split("@(?=[^@]*$)", );//分割位置：最後に出現する@。@を含まずsplitする。 aaaa と leetcode.com
String emails[i].split("(?=@[^@]*$)", );//分割位置：最後に出現する@の前。@を含んでsplitする。 aaaa と @leetcode.com
```

replaceではなくsplitを使って書いてみる  
```java
class Solution {
    public int numUniqueEmails(String[] emails) {
        Set<String> validAddresses = new HashSet<>();
        for (String email : emails) {
            if (!email.contains("@")) {
                continue;
            }
            String address[] = email.split("(?=@[^@]*$)", 2);
            address[0] = address[0].replaceFirst("\\+.*$", "");
            address[0] = address[0].replace(".", "");
            String address = address[0] + address[1];
            validAddresses.add(address);
        }
        return validAddresses.size();
    }
}
```


単に問題をどうやって解くかということだけを考えていたが、  
その処理のユースケースはどんなものがありうるかという視点が抜けていた。  
https://github.com/plushn/SWE-Arai60/pull/14/changes/62be885381060d38190094e7d507116711d1cfff  
RFC  
[RFC 5322 - Internet Message Format](https://datatracker.ietf.org/doc/html/rfc5322#section-3.4.1)  

>The locally  
>interpreted string is either a quoted-string or a dot-atom.  If the  
>string can be represented as a dot-atom (that is, it contains no  
>characters other than atext characters or "." surrounded by atext  
>characters), then the dot-atom form SHOULD be used and the quoted-  
>string form SHOULD NOT be used.  Comments and folding white space  
>SHOULD NOT be used around the "@" in the addr-spec.
>
RFC上では、@は一つだけとも限らず、"@@@@@"@leetcode.comなどローカルネーム内に@が含まれる場合もある。  
なので、ローカルとドメインを分ける位置は、「最初の@」ではなく最後の@にしなければいけない。  
STEP1で書いた8行目の正規表現では、最初に出現する@で区切るので、leetcodeの929を解くに限った話ではこれで問題ないが、  
RFCに沿って書き換えると以下の通り。  
```diff
--String localName = emails[i].replaceFirst("@.*$", "");
+++String localName = emails[i].replaceAll("@[^@]*$", "");
```
RFCと問題文のconstraintsを読み、  
・不正なメールアドレスの処理が足りていなかったので、追加  
・メールアドレスの長さは最大254字→正規化後の長さのチェックが必要   
・ローカルネームの先頭は+ではいけない  
・ドメインネームは.comで終わる  
不正なメールアドレスが来たときは、continueして次のアドレスの処理をすることとする。  
以前レビューコメントでもらったガード節をやってみる。  
```java
class Solution {
    public int numUniqueEmails(String[] emails) {
        Set<String> validAddresses = new HashSet<>();
        for (String email : emails) {
            if (!email.contains("@")) {
                continue;
            }
            if (email.startsWith("+")) {
                continue;
            }
            if (!email.endsWith(".com")) {
                continue;
            }
            String splitAddress[] = email.split("(?=@[^@]*$)", 2);
            splitAddress[0] = splitAddress[0].replaceAll("\\+.*$", "");
            splitAddress[0] = splitAddress[0].replace(".", "");
            if ((splitAddress[0] + splitAddress[1]).length() > 254) {
                continue;
            }
            validAddresses.add((splitAddress[0] + splitAddress[1]));
        }
        return validAddresses.size();
    }
}
```

#STEP3  
STEP2の最後に整理したコードでSTEP3を行う。
```java
class Solution {
    public int numUniqueEmails(String[] emails) {
        Set<String> validAddresses = new HashSet<>();
        for (String email : emails) {
            if (!email.contains("@")) {
                continue;
            }
            if (email.startsWith("+")) {
                continue;
            }
            if (!email.endsWith(".com")) {
                continue;
            }
            String splitAddress[] = email.split("(?=@[^@]*$)", 2);
            splitAddress[0] = splitAddress[0].replaceAll("\\+.*$", "");
            splitAddress[0] = splitAddress[0].replace(".", "");
            if ((splitAddress[0] + splitAddress[1]).length() > 254) {
                continue;
            }
            validAddresses.add((splitAddress[0] + splitAddress[1]));
        }
        return validAddresses.size();
    }
}
```
1回目  
Setの要素数はlengthではなくて、size  
拡張for文の型宣言が抜けている  
1回目その２  
java正規表現のエスケープは\\  
1回目その３  
文字列の文字数を返す時length()メソッドを使うのを忘れない。  
1回目その４  
変数名の複数形単数形の間違い→統一した方がいいのかな  
1回目その５  
その３同様  
エラーを直したが、テストケースが通らない  
1回目その６  
OK  
2回目  
OK  
3回目  
OK  

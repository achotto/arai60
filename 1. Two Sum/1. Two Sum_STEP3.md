  ```java
  class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> checked_numbers = new HashMap<>();
        for(int i = 0; i <= nums.length; i++){
            int complement = target - nums[i];
            if(checked_numbers.containsKey(complement)){
                return new int[]{checked_numbers.get(complement), i};
            }
            checked_numbers.put(nums[i], i);
        }
        throw new RuntimeException("No valid pair found");
    }
}
```
感想
STEP1では、配列を二つ使って総当たりする方法で書いた。Mapを使う方法は考えもつかなかった。
STEP2でほかの人の解答を見たら、Mapを使ったやり方をしている人が多かった。
そちらのほうが、時間計算量と空間計算量のどちらも少なくて済んでいるので、Mapを使ったやり方で書くことにした。

もし足してターゲットになる数のペアが入力値に存在しなかった時、どうするかについても考えてみた。
[-1, -1]を返すやり方もあると過去の解答で見たけれど、[-1, -1]が返ってきたときのイメージがわかなかった。（たくさんコードを見たことがないので、経験不足によると思いますが。。）
そのため、エラーメッセージを返す方法で考えた。

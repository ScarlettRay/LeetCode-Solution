思路一：

字符串p是模式字符串，字符串s是待匹配字符串，使用p对s进行匹配。联想到编辑距离那道题，使用两个指针i和j，i指向字符串s的第i个字符，j指向p的第j个字符，dp[i][j]=true表示字符串s的前i个字符可以编辑成字符串p的第j个字符，则存在三种情况，使dp[i][j]成立。

同理，通配符匹配这道题可以使用相同的思路，‘？’可以看成对模式字符串p进行一次变换操作，‘*’匹配两个或者两个以上字符可以看成对模式字符串p进行插入操作，‘*’匹配一个字符可以看成对p进行一次变换操作，‘*’匹配0个字符可以看成对p进行一次删除操作。使用dp[i][j]=true来表示s的前i个字符、p的前j个字符匹配成功，若dp[i][j]=true，则有三种可能：

dp[i-1][j-1]=true，且s.charAt(i-1)==p.charAt(j-1); \
dp[i][j-1]=true，且p.charAt(j-1)=='*'，这时表示‘*’匹配了0 个字符； \
dp[i-1][j]=true，且p.charAt(j-1)=='*'，这时表示‘*’匹配了s的第i个字符； \
为了方便，字符串的第一个字符从1开始，代码如下：
```
private static boolean isMatch(String s,String p) {
	int m=s.length();
	int n=p.length();
	boolean[][] dp=new boolean[m+1][n+1];
		
	dp[0][0]=true;
	for(int j=1;j<=n;j++) dp[0][j]=dp[0][j-1]&&p.charAt(j-1)=='*';
	for(int i=1;i<=m;i++) {
		for(int j=1;j<=n;j++) {
			if(p.charAt(j-1)=='?') dp[i][j]=dp[i-1][j-1];
			else {
				dp[i][j]=(dp[i-1][j-1]&&s.charAt(i-1)==p.charAt(j-1))||
						(dp[i-1][j]&&p.charAt(j-1)=='*')||
						(dp[i][j-1]&&p.charAt(j-1)=='*');
			}
		}
	}
		
	return dp[m][n];
		
}
```

思路二：

使用贪婪算法。若模式字符串p的第j个字符是‘*’，则用 j_start=j,i_start=i 记录此时‘*’和字符串s第i个字符的位置，然后跳到‘*’的下一个字符，从p的j+1处，s的i处开始，进行字符匹配（此时相当于‘*’匹配了0 个字符）；若发现无法完成匹配，则利用j_start i_start记录的初始位置，从p的j+1处，s的i+1处进行匹配。。。如此循环下去，直到完成匹配。

代码如下：
```private static boolean isMatch(String s,String p) {
   	int m=s.length(),n=p.length(),i_start=-1,j_start=-1,i=0,j=0;
   	while(i<m) {
   		if(j<n&&p.charAt(j)=='*') {
   			j_start=j;
   			i_start=i;
   			j++;
   		}
   		else if(j<n&&(s.charAt(i)==p.charAt(j)||p.charAt(j)=='?')) {
   			i++;
   			j++;
   		}
   		else if(j_start>-1){
   			j=j_start;
   			i=i_start+1;
   		}
   		else {
   			return false;
   		}
   	}
   		
   	while(j<n&&p.charAt(j)=='*') j++;
   	return j==n;
   }
   
```
与动态规划相比，贪婪算法的空间复杂度是O(1)。

原文：https://blog.csdn.net/weixin_41681068/article/details/81950622 

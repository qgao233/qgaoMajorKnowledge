# 子序列模板

## 模板1：一维dp（最长递增子序列）

```
int n = array.length;  
int[] dp = new int[n];  
  
for (int i = 1; i < n; i++) {  
    for (int j = 0; j < i; j++) {  
	dp[i] = 最值(dp[i], dp[j] + ...)  
    }  
}  
```

## 模板2：二维dp（最长公共子序列、最长回文子序列）

```
int n = arr.length;  
int[][] dp = new dp[n][n];  
  
for (int i = 0; i < n; i++) {  
    for (int j = 1; j < n; j++) {  
	if (arr[i] == arr[j])   
	    dp[i][j] = dp[i][j] + ...  
	else  
	    dp[i][j] = 最值(...)  
    }  
}  
```
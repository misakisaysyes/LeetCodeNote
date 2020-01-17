# LeetCode Patten Summary

---

### 1.滑动窗口

<br/>
**1.Average Of Subarray Of Size K (easy)**

Given an array, find the average of all contiguous subarrays of size ‘K’ in it.
	
Example:
	
	input : Array = [1, 3, 2, 6, -1, 4, 1, 8, 2], K=5
	output : [2.2, 2.8, 2.4, 3.6, 2.8]
	
	(1+3+2+6−1)/5=>2.2
	(3+2+6−1+4)/5=>2.8
	(2+6−1+4+1)/5=>2.4
	...etc...
	
Solution:
	
	class AverageOfSubarrayOfSizeK{
		public:
		static vector<double> findAverages(int K, const vector<int>& arr) {
			vector<double> res(arr.size() - k + 1);
			double wdsum;//子列和
			int wdstart;//滑动窗口初始边
			int wdend;//滑动窗口末边，用于在循环中替代i
			for(wdsum = 0, wdstart = 0, wdend = 0; wdend < arr.size(); wdend++){
				wdsum += arr[wdend];
				if(wdend >= k-1){//判断滑动窗口末边满足的条件，调整初始边
					res.push_back(wdsum/5);
					wdsum -= arr[wdstart++];
				}
			}
			return res;
		}
	}

Time complexity O(N)

Space complexity O(1)

<br/><br/>
**2.Maximum Sum Subarry Of Size K (easy)**

Given an array of positive numbers and a positive number ‘k’, find the maximum sum of any contiguous subarray of size ‘k’.

Example 1:

	Input: [2, 1, 5, 1, 3, 2], k=3 
	Output: 9
	Explanation: Subarray with maximum sum is [5, 1, 3].

Example 2:

	Input: [2, 3, 4, 1, 5], k=2 
	Output: 7
	Explanation: Subarray with maximum sum is [3, 4].

Solution:

	class MaxSumSubArrayOfSizeK {
		public:
		static int findMaxSumSubArray(int k, const vector<int>& arr) {
			int maxSum = 0;
			int wdstart, wdend, subSum;
			for(wdstart = 0, wdend = 0, subSum = 0; wdend < arr.size(); wdend++){
				subSum += arr[wdend];
				if(wdend >= k-1){
					maxSum = maxSum > subSum ? maxSum : subSum;
					subSum -= arr[wdstart++];
				}
			}
			return maxSum;
		}
	};
Time complexity O(N)

Space complexity O(1)

定长滑动窗口写法：

1.设变量：targetRes, wdstart, wdend.且wdend为遍历arr的游标.

2.同(时)移(动)(相)同距(离)：一个循环解决,循环无条件移动wdend,满足条件移动wdstart,判定条件<font color=#CD5C5C> wdend>=k-1 </font>

<br/><br/>
**3.Smallest Subarray with a given sum (easy)**

Given an array of positive numbers and a positive number ‘S’, find the length of the smallest contiguous subarray whose sum is greater than or equal to ‘S’. Return 0, if no such subarray exists.

Example 1:

	Input: [2, 1, 5, 2, 3, 2], S=7 
	Output: 2
	Explanation: The smallest subarray with a sum great than or equal to '7' is [5, 2].

Example 2:

	Input: [2, 1, 5, 2, 8], S=7 
	Output: 1
	Explanation: The smallest subarray with a sum greater than or equal to '7' is [8].

Example 3:

	Input: [3, 4, 1, 1, 6], S=8 
	Output: 3
	Explanation: Smallest subarrays with a sum greater than or equal to '8' are [3, 4, 1] or [1, 1, 6].

Solution:
	
	class MinSizeSubArraySum {
		public:
		static int findMinSubArray(int S, const vector<int>& arr) {
	    	int minLen = numeric_limits<int>::max();
	    	int subSum, wdstart, wdend;
	    	for(subSum = 0 ,wdstart = 0 , wdend = 0; wdend < arr.size(); wdend++){
	      		subSum += arr[wdend];
	      		while(subSum >= S){
					subSum -= arr[wdstart++];
					//只有在第一次移动wdstart后才能用[1][2]计算窗口最小长度
					//没有bool变量指示wdstart是否为第一次移动的情况下不能将[1][2]写到外循环中
					//否则当刚开始进入循环时wdend == wdstart == 0 会造成minLen计算错误 
	        		int subLen = wdend - wdstart + 1;//[1]
	        		minLen = subLen < minLen ? subLen : minLen;//[2]
	      		}
	    	}
	    	if(wdstart)
	      		return minLen;
	    	else
	      		return -1;
	 	}
	};

Time complexity O(N) <font color=#6B8E23>//wdend与wdstart不是同时遍历完整个数组的，而是wdend和wdstart分别遍历一遍数组,故时间复杂度为O(2*N),约等于O(N).</font>

Space complexity O(1)

动长滑动窗口写法：

1.设变量：targetRes, wdstart, wdend.且wdend为遍历arr的游标.

2.不同(时)移(动)不同距(离)=>两个循环解决，外循环移动wdend, 内循环移动wdstart，判定条件与targetRes有关.

<br/><br/>
**4.Longest Substring with K Distinct Characters (medium)**

Given a string, find the length of the longest substring in it with no more than .

Example 1:

	Input: String="araaci", K=2
	Output: 4
	Explanation: The longest substring with no more than '2' distinct characters is "araa".

Example 2:

	Input: String="araaci", K=1
	Output: 2
	Explanation: The longest substring with no more than '1' distinct characters is "aa".

Example 3:

	Input: String="cbbebi", K=3
	Output: 5
	Explanation: The longest substrings with no more than '3' distinct characters are "cbbeb" & "bbebi".

Solution:  
	
	class LongestSubstringKDistinct {
		public:
		static int findLength(const string& str, int k) {
		    int maxLength = 0;
		    int subLen, wdstart, wdend;
		    unordered_map<char, int> buf;
		    for(wdstart = 0, wdend = 0; wdend < str.length(); wdend++){
		      	buf[str[wdend]]++;
		      	while((int) buf.size() > k){
		        	char leftchar = str[wdstart++];
		        	buf[leftchar]--;
		        	if(!buf[leftchar])
		          	buf.erase(leftchar);
		      	}
		      	//变长滑动窗口对比Q3
		      	//在滑动窗口的题目中wdend恒>=wdstart
		      	//求minSubLen时循环初始条件wdstart = wdend = 0会影响结果,[1][2]最好写在内循环
		      	//求maxSubLen时循环初始条件无影响,[1][2]写在外循环
		      	subLen = wdend - wdstart + 1; //[1]
		      	maxLength = subLen > maxLength ? subLen : maxLength; //[2]
		    }
		    return maxLength;
		}
	};

Time complexity O(N) <font color=#6B8E23>//wdend与wdstart不是同时遍历完整个数组的，而是wdend和wdstart分别遍历一遍数组,故时间复杂度为O(2*N),约等于O(N).</font>

Space complexity O(K) <font color=#6B8E23>//哈希表中会存k+1个字符</font>
	

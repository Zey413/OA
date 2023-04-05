# Array-Subsets(Array Partition)

## Original Problem
___
**Array Subsets**

Given an integer array, divide the array into 2 subsets A and B while respecting the following conditions:
* The intersection of A and B is null.
* The union A and B is equal to the original array.
* The number of elements in subset A is minimal.
* The sum of A's elements is greater than the sum of B's elements.
 
Return the subset A in increasing order where the sum of A's elements is greater than the sum of B's elements. If more than one subset exists, return the one with the maximal sum.
 
Example

_n_ = 5

_arr_ = [3, 7, 5, 6, 2]
 
The 2 subsets in _arr_ that satisfy the conditions for A are [5, 7] and [6, 7]:
* A is minimal (size 2)
* Sum(A) = (5 + 7) = 12 > Sum(B) = (2 + 3 + 6) = 11
* Sum(A) = (6 + 7) = 13 > Sum(B) = (2 + 3 + 5) = 10
* The intersection of A and B is null and their union is equal to arr.
* The subset A where the sum of its elements is maximal is [6, 7].
 
**Function Description**

Complete the subsetA function in the editor below.
 
subsetA has the following parameter(s):
* int arr[]: an integer array
Returns:
* int[] : an integer array with the values of subset A.
 
Constraints
* 1 ≤ n ≤ 10<sup>5</sup> 
* 1 ≤ arr[i] ≤ 10<sup>5</sup> (where 0 ≤ i < n)

**Input Format For Custom Testing**

The first line contains an integer, _n_, denoting the number of elements in the array.
Each line _i_ of the _n_ subsequent lines contains an integer, which is an element of _arr_.

**Sample Case 0**

Sample Input For Custom Testing

| STDIN | Function |
|-------|----------|
|6|arr[] size n = 6|
|5|arr[] = [5,|
|3|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3,|
|2|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2,|
|4|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4,|
|1|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1,|
|2|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2]|

Sample Output

| STDOUT | Function |
|--------|----------|
|4|A[] = [4,|
|5|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5]|

Explanation

_n_ = 6

_arr_ = [5, 3, 2, 4, 1, 2]
 
The subset of A that satisfies the conditions is [4, 5] :
* A is minimal (size 2)
* Sum(A) = (4 + 5)= 9 > Sum(B) = (1 + 2 + 2 +  3) = 8
* The intersection of A and B is null and their union is equal to _arr_.
* The subset A with the maximal sum is [4, 5].
 
**Sample Case 1**

Sample Input For Custom Testing

| STDIN | Function |
|-------|----------|
|5|arr[] size n = 5|
|4|arr[] = [4,|
|2|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2,|
|5|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5,|
|1|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1,|
|6|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6]|

Sample Output

| STDOUT | Function |
|--------|----------|
|5|A[] = [5,|
|6|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6]|

Explanation

_n_ = 5

_arr_ = [4, 2, 5, 1, 6]
 
The subset of A that satisfies the conditions is [5, 6]:
* A is minimal (size 2)
* Sum(A) = (5 + 6) = 11 > Sum(B) = (1 + 2 + 4) = 7
* Sum(A) = (4 + 6) = 10 > Sum(B) = (1 + 2 + 5) = 8
* The intersection of A and B is null and their union is equal to _arr_.
* The subset A with the maximal sum is [5, 6].

___

## Failed Attempts

An initial approach for the problem might be:
* Treat input _arr_ as B
* Sort B
* Let A = []
* While the sum of A is less than the sum of B, move the greatest element from B to A
* Reverse A to return in increasing order

By using this greedy algorithm that always moves the highest number, we make the sumA > sumB with the fewest number of elements. We use int64_t for sumA and sumB to prevent overflow because the worst case sum of _n_ occurances of the max value of _arr[i]_ is (10<sup>5</sup>)*(10<sup>5</sup>).

```cpp
std::vector<int> subsetA(std::vector<int> arr) {
    std::vector<int> a;
    sort(arr.begin(), arr.end()); // Sort input array as B.

    int64_t sumA = 0;
    int64_t sumB = std::accumulate(arr.begin(), arr.end(), (int64_t)0);

    // While sumA > sumB, pop back B (max value) and push back to A.
    while(sumA <= sumB) {
        int popped = arr.back();
        arr.pop_back();
        sumA += popped;
        sumB -= popped;
        a.push_back(popped);
    }
    
    // A is in decreasing order. Reverse to return in increasing order.
    reverse(a.begin(), a.end());
    return a;
}
```

This erroneous solution is accepted by HackerRank, despite not fulfilling the condition "The intersection of A and B is null." With this solution, an example input of [1, 1, 2, 3, 3, 8] would result in A=[3, 8] and B=[1, 1, 2, 3], so the intersection of A and B is [3]. The correct solution should be A=[2, 8] and B=[1, 1, 3, 3].

This condition requires moving numbers that appear multiple times to be an all or nothing operation in order to prevent the same number from appearing in both A and B. However, the above example also illustrates that always moving all occurances of the next greatest number doesn't result in the smallest number of elements in A; The first operation would move [8] from B to A, and then the next operation would move [3, 3] from B to A, resulting in an array A of size 3, failing the problem's third condition ("The number of elements in subset A is minimal").

With this restriction in mind, the next step might be to augment this approach with a "look ahead" feature; if the next greatest element in B occurs _n_ times, determine if the sum of the next _n-1_ elements is big enough to cause sumA > sumB if moved. While this works better for some cases, this strategy isn't perfect- certain solutions don't involve taking the largest value at all. This look ahead implementaiton for [5, 5, 5, 10, 10, 10, 11] would return A=[10, 10, 10, 11] instead of A=[10, 10, 10].

## Solution
One method is to treat this like a 0-1 knapsack problem. Identical numbers are bundled up into a single element of cost/weight (number of occurances) and value (sum of each number's value). Creating these bundles allows us to evaluate each group of numbers with an all or nothing approach, which also fulfils the null intersection requirement. The max capacity of our knapsack is the number of elements in A. We don't know what this is, only that it must be minimized, so we have to run a knapsack algorithm starting with a capacity of 1 and increasing the capcity until a solution is found. Each knapsack algorithm will give us the elements that give the maximum sum of A per given size of A. These elements will be checked against the conditions and the size will increase until a solution is found.
________________________________________________________________
By working out a few examples by hand you'll see that any greedy algorithm simply won't work:

[2,5,5,9] => [2,9] (we won't necessarily take the largest values)
[2,2,4,5,5,11] => [4,11] (we won't necessarily take the smallest values either)
[5,5,5,10,10,10,11] => [10,10,10] (and we won't even necessarily take any largest value)
I believe this is actually the knapsack problem, where we must maximize the value (find the longest subset length for B) that is smaller than a given weight (the sum of A, which can be assumed to be the total sum divided by 2). Once we find the items that belong to B, we can simply invert the answer to get the subset for A.
```cpp
vector<int> subsetA(vector<int> arr) {
	std::sort(arr.begin(), arr.end());
	
	// generate the knapsack items and find the max weight
	struct Item {
		int value, weight, actual;
	};
	vector<Item> items;
	int last = 0, max_weight = 0;
	for (int item : arr) {
		max_weight += item;
		if (items.size() == 0 || item != last) {
			items.push_back(Item{1, item, item});
		} else {
			items[items.size() - 1].value++;
			items[items.size() - 1].weight += item;
		}
		last = item;
	}
	max_weight = (max_weight - 1)/2;
	
	// iterative 0/1 knapsack
	vector<vector<int>> dp(items.size(), vector<int>(max_weight + 1, 0));
	for (int i = 0; i < items.size(); i++) {
		for (int weight = 0; weight <= max_weight; weight++) {
			int skip = (i == 0) ? 0 : dp[i - 1][weight];
			int include = 0;
			if (items[i].weight <= weight) {
				include = items[i].value;
				if (i > 0) include += dp[i - 1][weight - items[i].weight];
			}
			dp[i][weight] = std::max(include, skip);
		}
	}
	
	// retrieve the items that were included in B
	unordered_set<int> B;
	int weight = max_weight;
	for (int i = items.size() - 1; i > 0; i--) {
		if (dp[i - 1][weight] != dp[i][weight]) {
			B.insert(items[i].actual);
			weight -= items[i].weight;
		}
	}
	if (weight-items[0].weight>=0) B.insert(items[0].actual);
	
	// now invert B to get A
	vector<int> A;
	for (int num : arr) {
		if (B.find(num) == B.end()) A.push_back(num);
	}
	
	return A;
}
```

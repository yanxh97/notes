# 1 Binary Search

### Search for exact match

Closed Interval
```Java
int[] nums;
Arrays.sort(nums);
// Arrays.sort(nums, Collections.reverseOrder());
int n = nums.length;

int l=0, r= n-1;
while (l <= r){
	int m = l + (r - l) / 2;
	if (nums[m] == target) return m;
	else if (nums[m] > target) r = m-1;
	else l = m+1;
}
return -1;
```

Case Study

| ``l<=r`` or ``l<r`` | ``r=m-1`` or ``r=m`` | ``l=m+1`` or ``l=m`` | counterexample                                |
| ------------------- | -------------------- | -------------------- | --------------------------------------------- |
| ``l<r``             | ``r=m``              | ``l=m``              | ``n[r-1]<n[r]<target`` -- infinite loop       |
| ``l<r``             | ``r=m``              | ``l=m+1``            | ``n[r-1]<n[r]==target`` -- miss; can be fixed |
| ``l<r``             | ``r=m-1``            | ``l=m``              | ``n[r-1]<n[r]<target`` -- infinite loop       |
| ``l<r``             | ``r=m-1``            | ``l=m+1``            | ``n[r-1]<n[r]==target`` -- miss; can be fixed |
| ``l<=r``            | ``r=m``              | ``l=m``              | ``n[r-1]<n[r]<target`` -- infinite loop       |
| ``l<=r``            | ``r=m``              | ``l=m+1``            | ``target<n[l]`` -- infinite loop              |
| ``l<=r``            | ``r=m-1``            | ``l=m``              | ``n[r-1]<n[r]<target`` -- infinite loop       |
``l<r, r=m-1, l=m+1`` can be solved by modifying the return clause to be
``return nums[l]==target?l:-1``

### Search for bound

Left Closed Right Open version of exact match. Notice that ``r=m`` because we want to make sure ``[l,r)`` is still a valid search interval (consider ``m-1`` is exactly the target, let ``r=m-1`` will lose it!)
```java
int l=0, r=nums.length;
int m=0;
while (l < r){
	m=(l+r)/2;
	if (nums[m] == target) return m;
	else if (nums[m] < target) l=m+1;
	else r=m;
}
return -1;
```

Lower bound (closed) -- the first element that is larger or equal to target
Upper bound (open) -- the first element that is larger than target
Both these result index could be **out of bound** ! 

**lower bound** -- left closed right open
```Java
int[] nums;
Arrays.sort(nums);
// Arrays.sort(nums, Collections.reverseOrder());
int n = nums.length;

int l=0, r = n;
while (l < r){
	// m would never be out of range
	int m = l + (r - l) / 2;
	if (nums[m] == target) r = m;
	else if (nums[m] > target) r = m;
	else l = m+1;
}
return l;
```

**upper bound** -- left closed right open
```Java
int[] nums;
Arrays.sort(nums);
// Arrays.sort(nums, Collections.reverseOrder());
int n = nums.length;

int l=0, r = n;
while (l < r){
	int m = l + (r - l) / 2;
	if (nums[m] == target) l = m+1;
	else if (nums[m] > target) r = m;
	else l = m+1;
}
return l;
```

# 2 Binary Tree 

Recursive method is trivial. Iterative methods are tricky.
### In-order Traversal with stack

Imitate the **recursive** calling procedure. 

Visiting and dealing the node is not done closely in the timeline, therefore we have to deal with node **right after popping**, instead of before pushing.

1. Keep pushing the current node into the stack, visit its left child util it's null -- like going into ``helper(node.left)`` recursively
2. When reaching null node, pop the first node in the stack, add it to result, and let ``curr = node.right`` -- like coming out of ``helper(node.left)`` , dealing with the node and going into ``helper(node.right)``.
3. When ``curr`` is not null, going back to step 1; when null, we have finished the right tree of ``curr``'s parent, so it's time for the parent of ``curr``'s parent, which shall be done in step 2.
```Java
Deque<TreeNode> dq = new LinkedList<>();
List<Integer> ans = new LinkedList<>();
TreeNode curr = root;
while (curr != null || !dq.isEmpty()){
	if (curr != null){
		dq.offerFirst(curr);
		curr = curr.left;
	}
	else{
		curr = dq.pollFirst();
		ans.add(curr.val);
		curr = curr.right;
	}
}
return ans;
```

### Pre-order Traversal with stack

Since stack is FILO we want right node visited later than left node, so right should be pushed into it first.
```java
Deque<TreeNode> dq = new LinkedList<>();
List<Integer> ans = new LinkedList<>();
TreeNode tn = root;
if (tn == null) return ans;
// deal with node after pop
dq.offerFirst(tn);
while (!dq.isEmpty()){
	tn = dq.pollFirst();
	ans.add(tn.val);
	if (tn.right != null) dq.offerFirst(tn.right);
	if (tn.left != null) dq.offerFirst(tn.left);
}
return ans;
```

Imitate recursive call with stack
```java
Deque<TreeNode> dq = new LinkedList<>();
List<Integer> ans = new LinkedList<>();
TreeNode tn = root;
while (tn != null || !dq.isEmpty()){
	if (tn != null){
		dq.offerFirst(tn);
		ans.add(tn.val);
		tn = tn.left;
	}
	else{
		// coming out of the recursive call stack
		tn = dq.pollFirst();
		tn = tn.right;
	}
}
return ans;
```

### Post-order Traversal

One approach is do **root-right-left** like pre-order does and reverse the answer.
Another approach should be introduced below.

### Universal Traversal -- Dummy Element

It's strong advised **not to push null** elements into dequeue, therefore use some dummy element out of the question's given range. The dummy element means the latter node is read to be dealt right now.
Like ``i == null ? e == null : i.equals(e)``
```java
Deque<TreeNode> dq = new LinkedList<>();
List<Integer> ans = new LinkedList<>();
TreeNode tn = root;
TreeNode dummy = new TreeNode(1000);
if (root != null)
	dq.offerFirst(root);

while (!dq.isEmpty()){
	tn = dq.pollFirst();
	if (tn.val != 1000){
		// only visited, not operated
		// change the order of the following lines to achieve 
		// different order traversal
		dq.offerFirst(tn);
		dq.offerFirst(dummy);
		if (tn.right != null) dq.offerFirst(tn.right);
		if (tn.left != null) dq.offerFirst(tn.left);
	}
	else{
		tn = dq.pollFirst();
		ans.add(tn.val);
	}
}
return ans;
```


### Level Traversal

Deque approach is simple. How to solve it recursively? Use a propagating variable to record results, a propagating integer to record depth.

For bottom-up level traversal, you can simulate the reverse order by using stack call i.e. add to to results after the recursive call.


# 3 Two pointers and sliding windows

Keeping i and j in a while loop is hard to maintain, iterate j in a for loop, and update i in a inner while loop instead. 



# 4 Segment Tree

Build, update and query, all better in post order. Note that you have to be careful with tree index and interval boundary index. Basically tree index -> ``2*index+1, 2*index+2`` for left and right children, interval index use ``left, mid, right``

Lazy update.

**307 [Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/)** 
```Java
int[] segTree;

int len = 0;

private int build(int l, int r, int i, int[] nums){
	if (l == r){
		segTree[i] = nums[l];
		return nums[l];
	}
	int mid = l + (r-l)/2;
	int lresult = build(l, mid, 2*i+1, nums);
	int rresult = build(mid+1, r,2*i+2 , nums);
	segTree[i] = lresult + rresult;
	return segTree[i];
}

private int query(int l, int r, int i, int ql, int qr){
	if (l == ql && r == qr) return segTree[i];
	int mid = l + (r-l)/2;
	int lresult = 0;
	int rresult = 0;
	if (mid < ql){
		rresult = query(mid+1, r, 2*i+2, ql, qr);
	}
	else if (mid >= qr){
		lresult = query(l, mid, 2*i+1, ql, qr);
	}
	else{
		lresult = query(l, mid, 2*i+1, ql, mid);
		rresult = query(mid+1, r, 2*i+2, mid+1, qr);
	}
	return lresult + rresult;
}

private int updateSeg(int l, int r, int i, int index, int value){
	if (l == r && r == index) {
		segTree[i] = value;
		return segTree[i];
	}
	if (index < l || index > r) return segTree[i];
	int mid = l + (r-l)/2;
	int lresult = 0;
	int rresult = 0;
	lresult = updateSeg(l, mid, 2*i+1, index, value);
	rresult = updateSeg(mid+1, r, 2*i+2, index, value);
	segTree[i] = lresult + rresult;
	return lresult + rresult;
}

public NumArray(int[] nums) {
	len = nums.length;
	segTree = new int[len * 4];
	build(0, len-1,0, nums);
	//for (int i=0; i<segTree.length; i++) System.out.print(segTree[i] + " ");
	//System.out.println();
	//System.out.println(query(0, len-1, 0, 0, 4));
}

public void update(int index, int val) {
	updateSeg(0, len-1, 0, index, val);
}

public int sumRange(int left, int right) {
	return query(0, len-1, 0, left, right);
}
```


# 5 Union Find

Maintain an array `parent[]` which is initialized to be `parent[i] = i`.

### `find()` and `union()`
```Java
int find(int u){
	// with path compression
	return (parent[u] == u)?u:(parent[u] = find(parent[u]));
	// without path compression
	return parent[u] == u ? u : find(parent[u]);
}

void union(int u, int v){
	int pu = find(u);
	int pv = find(v);
	// not necessarily
	if (pu == pv) return;
	// to choose the parent between pu, pv
	// refer to the context
	parent[pu] = pv;
	
}
```

### Trade-offs

If we do not do path compression, i.e. doing some reversable union/join yet not loosing too much performance, having preference over the choice of parent, then the building of the tree can be optimized by choosing the root with higher precedence to be the parent of this connection. 


# 6 Amazon

**Minimize Maximum Parcels**: compares average with max
**Sum of Max of Subarrays:** monotonic stack
```Java
int len = arr.length;
Deque<Integer> dqIdx = new LinkedList<>();
Deque<Integer> dqVal = new LinkedList<>();
int ans = 0;
int curSum = 0;
int addTerm = 0;
int i = 0;
while(i<len){
	while(!dqVal.isEmpty() && dqVal.peekFirst() < arr[i]){
		int idxPop = dqIdx.pollFirst();
		int valPop = dqVal.pollFirst();
		// update curSum and addTerm
		int effectLen = idxPop - (dqIdx.isEmpty()?-1:dqIdx.peekFirst());
		int startLen = i - idxPop;
		addTerm -= effectLen * valPop;
		// startLen + ... + (startLen + effectLen - 1)
		curSum -= valPop * (startLen*2+effectLen-1)*effectLen/2;
	}
	int effectLen = i - (dqIdx.isEmpty()?-1:dqIdx.peekFirst());
	addTerm += effectLen * arr[i];
	curSum += arr[i] * (effectLen-1)*effectLen/2;
	curSum += addTerm;
	dqIdx.offerFirst(i);
	dqVal.offerFirst(arr[i]);
	ans += curSum;
	i++;
}
return ans;
```


Or update the sum whenever pop out elements from the stack,
using fomula like ``(a+b)*a*b/2``. 

**Find Capable of Winners**: each player, bigger, middle, smaller; find largest middle and largest smaller.  capable iff bigger > largest middle and middel > largest smaller. 

**Compute Maximum Network Throughput**： sort

**Compute Maximum Scheduled Tasks**:  sort and two pointers

**Count Idle Warehouse Robots:** Hashing

**Make Power Non-decreasing**：Greedy

**Find the longest possible regular expression**: find the last index s.t. ``x_i != z_i and y_i != z_i``

**Find Max Value**: sort

**Find the Longest Isolated Substring Length**: hashing

**Warehouse Allocation (Distribute)**: 

# 7 Conversion

**Primitive and boxed/wrapped**
- Primitive to boxed:  
```Java
Integer a = new Integer(123); // deprecated
Integer b = 123;              // Auto boxing
Integer c = Integer.valueOf(123);
Integer d = Integer.valueOf("123", 4); // 27
```
- Boxed to primitive:
```Java
Integer a = 123;
int b = a; // Auto unboxing
int c = a.intValue();
```
- Primitive to primitive, use ``float a = (float) b``, there may be data loss if you are narrowing (byte - short - int -long). But widening is fine. Remember widening: ``byte-short-int-long-float-double and char-int-long-float-double``.
- Boxed to boxed
```Java
Long a = Long.valueOf(Integer.valueOf(123));
// compiles because it first does auto unboxing and widening.
```
- String and primitive
```Java
int a = Integer.parseInt(str); // careful about overflow
int b = Integer.parseInt(str, base);
String c = String.valueOf(123);
```
- String to boxed
```Java
Integer a = Integer.valueOf(str, base);
String b = String.valueOf(Integer.valueOf(12));
```


**Array and List**

**Array and List with stream**


# 8 Topology sort

**310 Minimum Height Tree**: each iteration, remove all leaf nodes (whose degree is one) and update degrees.


# 9 Dynamic Programming

**309 Best Time to Buy and Sell Stock with Cooldown**: stock problem pattern: maintain a dp array to record the sate-profit relation, state includes holding shares, not holding shares with freeze, not holding shares without freeze, and the initial value of "holding shares" state should be `Integer.MIN_VALUE`.

**312 [Burst Balloons](https://leetcode.com/problems/burst-balloons/): Similar to matrix-chain multiplication problem ``(1,3)|(3,5)|(5,8)|(8,1)``
, the state `dp[i][j]` transition formula should based on all possible **last merged** element within `[i,j]` intervals. ``dp[i][j] = min(dp[i][k-1] + dp[k+1][j] + nums[k] * nums[i-1] * nums[j+1])``

**313 [Super Ugly Number](https://leetcode.com/problems/super-ugly-number/)**: one array to keep the results, another array to keep the index of each prime factor's next element to multiply with.

# 10 Merge Sort

**315 [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)** Like counting inversion, during merging, if the left half is merging into the result, add right half iterator to the corresponding inversion count array. 


# 11 Monotonic Stack

**316 [Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/)** Greedily choose smallest character (from a..z) as the first character, verify feasibility by checking the comparing the first index of chosen character and last index of other character. **Monotonic stack** way: make the stack monotonically increasing from bottom to top, and keep popping if the top element is greater than the current element **and** the last index of top element is greater than the current index (to ensure feasibility).

**2818 [Apply Operations to Maximize Score](https://leetcode.com/problems/apply-operations-to-maximize-score/)** Use monotonic stack to compute ``left[]`` and ``right[]`` array to determines the subarrays that have current element as the highest prime score. Use fast exponential to compute results. 


# 12 LinkedList

**116&117 [Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)** Constant extra space is required. Use the next-connected results of previous layer to update the current layer. Do it in a iterative way by introducing a ``childHead`` dummy head.

**138 [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)** First run: reformat the list as ``originNode->newNode->originNode-> newNode -> ....`` Next run: update new nodes' next pointer to the corresponding new node. Last run: split it into two linked list.

# 13 Transformer

Encoder and Decoder structure.

### Encoder 

Encoder: stack of $N=6$ identical layer. Each layer is a self-multi-head-attention sublayer plus an ffn sublayer:

1. **Input**

Embedding and additive positional encoding is performed before entering the first layer.  The input $X$ has shape of $${n_{in} \times d_{model}}$$ where $d_{model}$ depends on the embedding model.

2. **Scaled Dot-Product Attention**
  
  The input is described above. This is a sub-part of multi-head attention.
$$Q = XW_Q^{enc}, K = XW_K^{enc}, V = XW_V^{enc}$$
$$Attention(Q,K,V) = softmax(\frac{QK^T}{\sqrt{d_k}})V$$

Let's suppose $W_Q^{enc} \in \mathbb{R}^{d_{model} \times d_q}, W_K^{enc} \in \mathbb{R}^{d_{model} \times d_k}$ to allow matrix multiplication $QK^T$ we have to let $d_q = d_k$. $W_V^{enc} \in \mathbb{R}^{d_{model} \times d_v}$ in single-head attention, it is usually the case that $d_k = d_v = d_{model}$. 

The output of this part has shape $n_{in} \times d_v$.


3. **Multi-head attention sub-layer**

The input $X_Q=X_K=X_V=X$ is described in 1.
$$Multihead(X_Q,X_K,X_V) = Concat(head_1 .. head_h)W_O$$
$$\text{where } head_i = Attention(X_QW_{Qi}, X_KW_{Ki}, X_VW_{Vi})$$
$W_O$ is in the shape of ${h \cdot d_v \times d_{model}}$.  It's for simplicity that the paper chooses $d_k = d_v = d_{model}/h$ but not necessarily. $W_O$ would always project the output to the original size to allow add and norm block. Each head shares input but does not share the weights.  

The output of this part has shape $n_{in} \times d_{model}$

4. **Add and norm after multi-head attention**
Residual add and layer normalization.

5. **FFN, add and norm**
$$FFN(x) = max(0, xW_1 + b_1)W_2+b_2$$
It's simply a one-hidden-layer neural network, with a trailing add and norm block. The linear transformations are **the same positional wise** and they use **different parameters from layer to layer**. 

The output shape must stay the same as input.

6. **output**: after stack of $N=6$ identical block, we have an output size of $n_{in} \times d_{model}$ which would be then fed into decoder sub-layer 2 as the fixed, unprojected K, V, and K,V are the same for all 6 layers.

### Decoder

Similarly stack of $N=6$ layer, the input from encoder is fixed over all 6 layers.

1. **Input**: current generated output sequence 
$$X \in \mathbb{R}^{n_{out} \times d_{model}}$$
2. **Masked Multi-Head attention**: for each head, the $QK^T$ would add an upper triangular matrix
$$\begin{matrix} 
0 & -\infty & \dots & -\infty \\
0 & 0 & \dots & -\infty\\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \dots & 0
\end{matrix}$$
to prevent leftward information flow in the decoder to preserve the auto-regressive property.

3. **encoder-decoder multi-head attention**
The input
$$X_K=X_V=X_{enc} \in \mathbb{R}^{n_{in} \times d_{model}}$$ are identical and from encoder, while 
$$X_Q = X_{dec} \in \mathbb{R}^{n_{out} \times d_{model}}$$ are from the previous sub-layer masked multi-head attention. 
$$Q = X_{dec}W_Q^{cross}, K = X_{enc}W_K^{cross}, V = X_{enc}W_V^{cross}$$
$$Attention(Q,K,V) = softmax(\frac{QK^T}{\sqrt{d_k}})V$$
$$MultiHeadAttetion(Q,K,V) = Concat(head_1,...head_h) W_O$$
The output of this part has shape $n_{out} \times d_{model}$

4. **FFN, add and norm**  like before.

5. **output**: after stack of $N=6$ identical block, we have an output size of $n_{out} \times d_{model}$ which would be then fed into decoder sub-layer 2 as the fixed, unprojected K, V. Then this output is passing through a **linear block** (of shape $d_{model} \times |V|$) and a **row-wise softmax block** to predict probabilities. The next token is retrieved from the last row, so during inference time, there is no need to calculate the whole output for the last two block (the previous stacked layer is still necessary).


# 14 Java io/nio with file system

## (buffered) IO flush 

In old io, the ``flush()`` would not do anything with the file system instantaneously due to mmap. At this time, when you ``ls`` or ``vi`` the corresponding file, it would return the illusion value. It's usually upon OS to decide when to write the content to the disk.

## RAF, channel, mappedByteBuffer and force()
``force()`` allow content in the page cache flush to file system.
mmap reduce write system calls overhead by batching and delay.

## Cache read and write policy
cache alongside
cache read 

## Useful cmd
``strace`` system call trace
``jps`` jvm pid
``pcstat`` page cache stat
``lsof``



# 15 Hungarian Algorithm

Key: find an augmented path and switch between matched edges and unmatched edges.

```java
List<Integer>[] graph;
int alg(){
	int[] pre = new int[n];
	boolean[] visited = new boolean[m];
	int matchCount = 0;
	Arrays.fill(pre, -1);
	for (int i=0; i<n; i++){
		Arrays.fill(visitd, false);
		if (dfs(i)) matchCount++;
	}
}
boolean dfs(int i){
	for (int j:graph[i]){
		if (visited[j]) continue;
		// why we can ignore j in the following search?
		// because once there's no augmented path like i-j-..
		// then there won't be another augmented path like
		// i- .. -j-p-..-q
		visited[j] = true;
		if (pre[j] == -1 || dfs(pre[j]))
			pre[j] = i;
			return true;
	}
	return false;
}
```
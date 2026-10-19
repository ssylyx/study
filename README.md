# 面试
# C++ interview:
- https://github.com/huihut/interview#review-of-brush-questions-website
- https://github.com/EricPengShuai/Interview
- https://www.cnblogs.com/autodriver/p/19769163

# 解题模板
## 二分查找

面试默认用"左闭右开 + < + check 划分"，因为：  
1. 它就是 lower_bound 的实现，STL 同款，讲起来有依据
2. left == right 结束时答案直接就是 left，没有"返回 left 还是 right"的困惑
3. 不容易因 right = mid - 1 写错导致死循环


```cpp
int binarySearch(vector<int>& nums, int target) {
    int left = 0, right = nums.size();       // 开区间 [left, right)
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) left = mid + 1;  // mid 排除
        else right = mid;                         // mid 保留在 [left, right)
    }
    return left;   // 第一个 >= target 的位置
}
```

## 归并排序
```cpp
#include <vector>
using namespace std;

// 合并两个有序区间 [l, mid] 和 [mid+1, r]
void merge(vector<int>& nums, int l, int mid, int r) {
    vector<int> temp(r - l + 1);          // 临时数组
    int i = l, j = mid + 1, k = 0;

    while (i <= mid && j <= r) {          // 双指针取较小者
        if (nums[i] <= nums[j]) temp[k++] = nums[i++];
        else temp[k++] = nums[j++];
    }
    while (i <= mid) temp[k++] = nums[i++];   // 左半剩余
    while (j <= r)   temp[k++] = nums[j++];   // 右半剩余

    for (int t = 0; t < k; ++t)           // 写回原数组
        nums[l + t] = temp[t];
}

void mergeSort(vector<int>& nums, int l, int r) {
    if (l >= r) return;                   // 单个元素天然有序
    int mid = l + (r - l) / 2;            // 防溢出
    mergeSort(nums, l, mid);              // 分：排左半
    mergeSort(nums, mid + 1, r);          // 分：排右半
    merge(nums, l, mid, r);               // 合：合并
}

// 对外接口
void mergeSort(vector<int>& nums) {
    if (nums.size() > 1) mergeSort(nums, 0, nums.size() - 1);
}

```cpp

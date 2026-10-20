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

```

## 快速排序
### 实现一：Hoare 分区（推荐，效率高）
- 单指针扫描：i 标记"下一个小于 pivot 的位置"，j 负责扫描
- 递归边界是 [l, p-1] 和 [p+1, r]（pivot 已就位）
```cpp
#include <vector>
using namespace std;

// 分区：返回 pivot 最终位置；左边都 <= pivot，右边都 >= pivot
int partition(vector<int>& nums, int l, int r) {
    int pivot = nums[l + (r - l) / 2];   // 取中间值做基准（也可随机）
    int i = l - 1, j = r + 1;

    while (true) {
        do { i++; } while (nums[i] < pivot);    // 从左找 >= pivot 的
        do { j--; } while (nums[j] > pivot);    // 从右找 <= pivot 的
        if (i >= j) return j;                   // 相遇则分区完成
        swap(nums[i], nums[j]);                 // 交换，让两边各就各位
    }
}

void quickSort(vector<int>& nums, int l, int r) {
    if (l >= r) return;
    int p = partition(nums, l, r);
    quickSort(nums, l, p);        // 左半 [l, p]
    quickSort(nums, p + 1, r);    // 右半 [p+1, r]
}

void quickSort(vector<int>& nums) {
    if (nums.size() > 1) quickSort(nums, 0, nums.size() - 1);
}
```
### 实现二：Lomuto 分区（更简单好背）
- 单指针扫描：i 标记"下一个小于 pivot 的位置"，j 负责扫描
- 递归边界是 [l, p-1] 和 [p+1, r]（pivot 已就位）
```cpp
int partitionLomuto(vector<int>& nums, int l, int r) {
    int pivot = nums[r];           // 取最右做基准
    int i = l;                     // i 左边都是 <= pivot 的
    for (int j = l; j < r; ++j) {
        if (nums[j] < pivot) {
            swap(nums[i], nums[j]);
            i++;
        }
    }
    swap(nums[i], nums[r]);        // pivot 放到正确位置
    return i;
}

void quickSort(vector<int>& nums, int l, int r) {
    if (l >= r) return;
    int p = partitionLomuto(nums, l, r);
    quickSort(nums, l, p - 1);
    quickSort(nums, p + 1, r);
}
```
## 算法知识
### Floyd判圈算法
https://share.google/aimode/xe6E0TERBVTmoL5p6

### 矩阵旋转
```cpp
变种	操作	例：[[1,2,3],[4,5,6],[7,8,9]]
顺时针 90°	转置 + 反转每行	[[7,4,1],[8,5,2],[9,6,3]]
逆时针 90°	转置 + 反转每列	[[3,6,9],[2,5,8],[1,4,7]]
180°	反转每行 + 反转每列（或上下+左右翻转）	[[9,8,7],[6,5,4],[3,2,1]]
转置	只做对角线翻转	[[1,4,7],[2,5,8],[3,6,9]]
左右镜像	只反转每行	[[3,2,1],[6,5,4],[9,8,7]]
上下镜像	只反转每列	[[7,8,9],[4,5,6],[1,2,3]]
```

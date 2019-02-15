## 1. LeetCode 836 [Rectangle Overlap](https://leetcode.com/problems/rectangle-overlap/)

```c++
class Solution {
public:
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
        return (min(rec1[2], rec2[2]) > max(rec1[0], rec2[0])
               && min(rec1[3], rec2[3]) > max(rec1[1], rec2[1]));
    }
};
```



## 2. LeetCode 223 [Rectangle Area](https://leetcode.com/problems/rectangle-area/)

```c++
class Solution {
public:
    int computeArea(int A, int B, int C, int D, int E, int F, int G, int H) {
        long width = (long)min(C, G) - (long)max(A, E);
        long height = (long)min(D, H) - (long)max(B, F);
        
        long area = (C - A) * (D - B) + (G - E) * (H - F);
        
        if (width > 0 && height > 0) {
            return area - width * height;
        }
        
        return area;
    }
};
```



## 3. LeetCode 991 [Broken Calculator](https://leetcode.com/problems/broken-calculator/)

```c++
class Solution {
public:
    int brokenCalc(int X, int Y) {
        int ans = 0;
        
        while (Y > X) {
            ans++;
            if (Y % 2 == 1)
                Y++;
            else
                Y /= 2;
        }

        return ans + X - Y;
    }
};
```



## 4. LeetCode 400 [Nth Digit](https://leetcode.com/problems/nth-digit/)

```c++
class Solution {
public:
    int findNthDigit(int n) {
        int len = 1;
		long count = 9;
		int start = 1;

		while (n > len * count) {
			n -= len * count;
			len += 1;
			count *= 10;
			start *= 10;
		}

		start += (n - 1) / len;
		string s = to_string(start);
        
		return (s[(n-1) % len]) - '0';
    }
};
```



## 5. LeetCode 573 [Squirrel Simulation](https://leetcode.com/problems/squirrel-simulation/)

```c++
class Solution {
public:
    int minDistance(int height, int width, vector<int>& tree, vector<int>& squirrel, vector<vector<int>>& nuts) {
        int total_dist = 0, d = INT_MIN;
        
        for (auto nut : nuts) {
            total_dist += distance(nut, tree) * 2;
            d = max(d, distance(nut, tree) - distance(nut, squirrel));
        }
        
        return total_dist - d;
    }
    
private:
    int distance(vector<int> &a, vector<int> &b) {
        return abs(a[0] - b[0]) + abs(a[1] - b[1]);
    }
};
```


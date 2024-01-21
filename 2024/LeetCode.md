3. 无重复字符的最长子串
```ts
function lengthOfLongestSubstring(s: string): number {
    if (s.length === 0) {
        return 0
    }
    const map = new Map()
    let max = 0
    let left = 0

    for (let i = 0; i < s.length; i++) {
        if (map.has(s[i])) {
            // 在map中如果有相同的字符，则将指针移动到这个字符的后一位
            // pwwkew
            // i = 5 时，left移动到 2 + 1，就是第二个w的下一位k的坐标处
            left = Math.max(left, map.get(s[i]) + 1)

        }
        map.set(s[i], i)
        max = Math.max(max, i - left + 1)
    }

    return max
};
```
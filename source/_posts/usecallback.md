---
title: useCallback 用法
date: 2020-04-14 14:22:32
tags:
---

# useCallback 用法

## 用法一

结合 `memo` `useMemo` `useCallback` 可以当子组件的更新产生性能问题的时候使用，减少子组件的更新

```js
// 子组件 频繁的重新渲染导致性能问题 例如 树组件
const Child = React.memo((props) => {
  return <div>memoized component</div>;
});

const Parent = () => {
  const a = useMemo(1, []);
  const b = useCallback(() => {}, []);

  return (
    <div>
      <Child a={a} b={b} />
    </div>
  );
};
```

## 用法二

做节流时，如果是状态更新就会产生新的节流函数
下面是 antd 中搜索下拉多选的案例

```js
const Page = () => {
  const [value, setValue] = useState("");

  const handleSearch = useCallback(
    debounce(() => {}, 800),
    []
  );

  return (
    <div>
      <Select
        value={value}
        onSearch={handleSearch}
        onChange={(e) => setValue(e.target.value)}
      />
    </div>
  );
};
```

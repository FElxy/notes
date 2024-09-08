

```
import React, { useRef, useCallback } from 'react';
 
// 子组件
const ChildComponent = React.forwardRef((props, ref) => {
  const doSomething = useCallback(() => {
    console.log('Do something in the child component');
  }, []);
 
  // 当组件挂载后，将这个组件的实例传递给父组件
  React.useImperativeHandle(ref, () => ({
    doSomething
  }));
 
  return <div>Child Component</div>;
});
 
// 父组件
const ParentComponent = () => {
  const childRef = useRef(null);
 
  const handleClick = () => {
    if (childRef.current) {
      childRef.current.doSomething();
    }
  };
 
  return (
    <>
      <button onClick={handleClick}>Call Child Method</button>
      <ChildComponent ref={childRef} />
    </>
  );
};
 
export default ParentComponent;
```


> https://www.cnblogs.com/openmind-ink/p/18246686
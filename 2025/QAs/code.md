## call, apply, bind
```
Function.prototype.newCall = function(ctx, ...args) {

}
```

## debounce
```
function debounce(fn, delay = 200) {
    let timer = null
    return function(...args) {
        let ctx = this
        clearTimeout(timer)
        timer = setTimeout(() => {
            fn.apply(this, args)
        }, delay)
    }
}
```

## 实现 convert 方法，把原始 list 转换成树形结构，要求尽可能降低时间复杂度

## Object.create
```
function newCreate(proto) {
    let obj = {}
    Object.setPrototypeOf(obj, proto)
    return obj
}
function myCreate(proto) {
  function F() {}
  F.prototype = proto;
  return new F();
}

```

## instanceOf
```
function newInstanceOf(obj, constructor) {
    let proto = Object.getPrototypeOf(obj)
    while (proto) {
        if ( proto === constructor.prototype) {
            return true
        } else {
            proto = Object.getPrototypeOf(proto)
        }
    }
    return false
}
```

## 判断DOM在可视区范围内
判断 DOM 元素是否在可视区域，现代推荐使用 IntersectionObserver，它是浏览器提供的异步回调机制，无需监听滚动事件，性能最好。

如果需要兼容性更好的方案，可以使用 getBoundingClientRect 获取元素相对视口的位置，再与视口宽高比较判断。

## 判断一个单向链表是否是循环链表
```
function isCyclicLinkedList(head) {
  if (!head) {
    return false;
  }

  let slow = head;
  let fast = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;

    if (slow === fast) {
      return true;
    }
  }

  return false;
}
```

## 深拷贝

## 千分符

## 手写promise

## 手写JSON.parse JSON.stringify
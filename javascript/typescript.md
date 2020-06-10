# TypeScript

接口

泛型

d.ts

类型声明

优点：

静态类型声明

提高代码可读性和可维护性。 在编译阶段提前检查类型错误。

接口

泛型：为成员提供有意义的约束，包括参数、方法、返回值等。

```text
// 创建一个泛型类
class Queue<T> {
  private data: T[] = [];
  push = (item: T) => this.data.push(item);
  pop = (): T | undefined => this.data.shift();
}

// 简单的使用
const queue = new Queue<number>();
queue.push(0);
queue.push('1'); // Error：不能推入一个 `string`，只有 number 类型被允许
```


# 地址

`address`是Move语言中的一个内置类型，用于表示存储中的位置（有时也称为账户）。一个`address`值是一个256位（32字节）的标识符。Move使用地址来区分[模块](../modules.md)的包，每个包都有自己的地址和模块。特定的Move部署也可能使用`address`值进行[存储](../abilities.md#key)操作。

> 对于Sui来说，`address`用于表示"账户"，也通过强类型包装器（使用`sui::object::UID`和`sui::object::ID`）表示对象。

虽然`address`在底层是一个256位整数，但Move地址是有意不透明的——它们不能从整数创建，不支持算术运算，也不能被修改。特定的Move部署可能有`native`函数来启用其中一些操作（例如，从字节`vector<u8>`创建一个`address`），但这些不是Move语言本身的一部分。

虽然存在运行时地址值（`address`类型的值），但它们在运行时 _不能_ 用于访问模块。

## 地址及其语法

地址有两种形式，命名的或数字的。命名地址的语法遵循Move中任何命名标识符的相同规则。数字地址的语法不限于十六进制编码的值，任何有效的[`u256`数值](./integers.md)都可以用作地址值，例如，`42`、`0xCAFE`和`10_000`都是有效的数字地址字面量。

为了区分地址是否在表达式上下文中使用，使用地址的语法根据使用的上下文而有所不同：

- 当地址作为表达式使用时，地址必须以`@`字符为前缀，即[`@<数值>`](./integers.md)或`@<命名地址标识符>`。
- 在表达式上下文之外，地址可以不带前导`@`字符书写，即[`<数值>`](./integers.md)或`<命名地址标识符>`。

一般来说，你可以把`@`看作是一个操作符，它将地址从命名空间项转换为表达式项。

## 命名地址

命名地址是一个特性，允许在使用地址的任何地方使用标识符来代替数值，而不仅仅是在值级别。命名地址在Move包中被声明和绑定为顶层元素（在模块和脚本之外），或作为参数传递给Move编译器。

命名地址只存在于源语言级别，在字节码级别将完全被其值替代。因此，应该通过模块的命名地址而不是编译期间分配给命名地址的数值来访问模块和模块成员。所以虽然`use my_addr::foo`等同于`use 0x2::foo`（如果`my_addr`被分配为`0x2`），但最佳实践是始终使用`my_addr`名称。

### 示例

```move
// 0x0000000000000000000000000000000000000000000000000000000000000001的简写
let a1: address = @0x1;
// 0x0000000000000000000000000000000000000000000000000000000000000042的简写
let a2: address = @0x42;
// 0x00000000000000000000000000000000000000000000000000000000DEADBEEF的简写
let a3: address = @0xDEADBEEF;
// 0x000000000000000000000000000000000000000000000000000000000000000A的简写
let a4: address = @0x0000000000000000000000000000000A;
// 将命名地址`std`的值赋给`a5`
let a5: address = @std;
// 任何有效的数值都可以用作地址
let a6: address = @66;
let a7: address = @42_000;

module 66::some_module {   // 不在表达式上下文中，所以不需要@
    use 0x1::other_module; // 不在表达式上下文中，所以不需要@
    use std::vector;       // 可以使用命名地址作为命名空间项
    ...
}

module std::other_module {  // 声明模块时可以使用命名地址
    ...
}
```
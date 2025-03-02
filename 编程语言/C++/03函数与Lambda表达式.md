# 函数与Lambda表达式

[toc]

# 函数

---

## 一、函数基础

---

### 1. 声明与定义

#### (1) 基本语法与默认参数

```cpp
// 默认参数必须从右向左连续定义
void log_message(const string& msg, bool timestamp = true, ostream& out = cout) {
    if (timestamp) {
        out << "[" << get_current_time() << "] ";
    }
    out << msg << endl;
}

// 尾置返回类型（C++11）
auto add(int a, double b) -> double {
    return a + b;
}

// 函数签名唯一性示例
class Widget {
public:
    void process() const;   // const成员函数
    void process(int);      // 参数类型不同
    void process() &;       // 左值引用限定符（C++11）
    void process() &&;      // 右值引用限定符
};
```

**关键特性**：
- 默认参数在声明处指定（头文件中）
- 返回类型推导支持复杂场景（如模板函数）
- 函数重载考虑const修饰符和引用限定符

---

### 2. 参数传递机制

#### (1) 传值 vs 传引用

```cpp
struct BigData { int buffer[1024]; };

// 传值：拷贝整个结构体
void process_value(BigData data) {
    // 操作副本
}

// 传const引用：零拷贝
void process_cref(const BigData& data) {
    // 只读访问原始数据
}

// 右值引用：移动语义优化
void consume_data(BigData&& data) {
    // 接管资源所有权
}

// 完美转发模板
template<typename T>
void relay(T&& arg) {
    process(std::forward<T>(arg));
}
```

**性能对比**：
| 传递方式 | 适用场景               | 拷贝次数 | 可修改性   |
| -------- | ---------------------- | -------- | ---------- |
| 传值     | 小对象（<=寄存器大小） | 1次      | 副本可修改 |
| const T& | 大对象只读访问         | 0次      | 不可修改   |
| T&       | 需要修改原始对象       | 0次      | 可修改     |
| T&&      | 资源转移场景           | 0次      | 转移所有权 |

---

### 3. 函数重载

#### (1) 重载解析规则

```cpp
void print(int num) { cout << "Integer: " << num << endl; }
void print(double num) { cout << "Double: " << num << endl; }
void print(const string& str) { cout << "String: " << str << endl; }

// 调用示例
print(42);          // 精确匹配print(int)
print(3.14f);       // 提升到double
print("hello");     // 转换到string

// 二义性示例
void ambiguous(int);
void ambiguous(double);
// ambiguous(5L);   // 错误：long->int和long->double优先级相同
```

**重载优先级**：
1. 精确匹配（类型相同）
2. 类型提升（char→int, float→double）
3. 标准转换（int→double, 派生类→基类）
4. 用户定义转换

---

## 二、函数高级特性

---

### 1. 编译期函数

#### (1) constexpr函数

```cpp
constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

constexpr int fib(int n) {
    if (n <= 1) return n;
    return fib(n-1) + fib(n-2);
}

// 编译期计算
constexpr int fact_10 = factorial(10);  // 3628800
static_assert(fib(10) == 55, "Fibonacci error");
```

**C++版本演进**：
- C++11：单一return语句
- C++14：支持局部变量、循环
- C++20：支持虚函数、try-catch

---

### 2. 可调用对象

#### (1) 函数对象与std::function

```cpp
struct Adder {
    int base;
    explicit Adder(int b) : base(b) {}
    int operator()(int x) const { return base + x; }
};

// 使用示例
Adder add5(5);
cout << add5(3) << endl;  // 输出8

// 类型擦除包装
function<int(int)> callback;
callback = add5;
callback = [](int x) { return x * x; };
```

**性能对比**：
| 可调用类型    | 调用开销 | 内存占用 | 内联可能性 |
| ------------- | -------- | -------- | ---------- |
| 函数指针      | 高       | 小       | 低         |
| 函数对象      | 低       | 可变     | 高         |
| std::function | 中       | 较大     | 低         |

---

## 三、Lambda表达式

---

### 1. 基本语法与捕获列表

```cpp
// 值捕获 vs 引用捕获
int base = 10;
auto lambda1 = [base](int x) { return x + base; };  // 值捕获
auto lambda2 = [&base](int x) { base += x; };       // 引用捕获

// 初始化捕获（C++14）
auto ptr = make_unique<int>(42);
auto lambda3 = [p = move(ptr)] { return *p; };

// 可变lambda（mutable）
auto counter = [n = 0]() mutable { return n++; };
```

**捕获方式**：
- `[]`：不捕获任何变量
- `[=]`：值捕获所有外部变量（不推荐，易导致悬垂引用）
- `[&]`：引用捕获所有变量（需谨慎生命周期管理）
- `[var]`：显式值捕获特定变量
- `[this]`：捕获当前对象指针

---

### 2. 泛型Lambda（C++14/20）

```cpp
// C++14自动类型推导
auto generic_lambda = [](auto x, auto y) {
    return x + y;
};

// C++20模板参数列表
auto template_lambda = []<typename T>(T a, T b) {
    return a * b;
};

// 使用示例
cout << generic_lambda(3, 4.5) << endl;    // 7.5
cout << template_lambda(2.5, 3.0) << endl; // 7.5
```

---

## 关键概念总结表

| 特性          | 版本要求 | 适用场景               | 性能特点       |
| ------------- | -------- | ---------------------- | -------------- |
| 默认参数      | C++03    | 简化接口，提供默认行为 | 无额外开销     |
| 尾置返回类型  | C++11    | 复杂返回类型推导       | 编译期决定     |
| constexpr函数 | C++11    | 编译期计算             | 零运行时开销   |
| 右值引用参数  | C++11    | 移动语义优化           | 避免资源拷贝   |
| 泛型Lambda    | C++14    | 模板函数简化           | 类型安全推导   |
| 模板Lambda    | C++20    | 明确类型约束           | 更好的类型控制 |

---

## 最佳实践

1. **参数传递选择**：
   ```cpp
   void process(const BigData& data);  // 大对象用const引用
   void modify(BigData& data);         // 需要修改用非const引用
   void sink(BigData&& data);          // 资源转移用右值引用
   ```

2. **重载设计原则**：
   ```cpp
   // 提供不同抽象级别的接口
   void draw(const Shape&);          // 通用接口
   void draw(Circle);                // 优化特定类型
   ```

3. **Lambda捕获安全**：
   ```cpp
   auto make_worker() {
       int local = 42;
       return [=] { return local; };  // 值捕获避免悬垂引用
   }
   ```

4. **异常安全保证**：
   ```cpp
   void safe_op() noexcept {  // C++11显式声明不抛异常
       // 关键系统函数
   }
   ```

5. **元编程辅助**：
   ```cpp
   template<typename F>
   auto log_duration(F&& func) {  // 通用函数包装器
       auto start = steady_clock::now();
       auto result = forward<F>(func)();
       auto dur = steady_clock::now() - start;
       cout << "Duration: " << dur.count() << "ns\n";
       return result;
   }
   ```

---

# 函数高级特性

---

## 一、编译期与运行时优化

---

### 1. `inline`的语义演进

```cpp
// C++03：编译器优化建议（可能被忽略）
inline int add(int a, int b) {
    return a + b;
}

// 现代C++：链接器符号合并保证
class MathUtils {
public:
    inline static double pi() { // C++17内联变量
        return 3.1415926;
    }
};

// 实际应用场景
template<typename T>
inline T max(T a, T b) {  // 头文件中必须inline避免重复定义
    return (a > b) ? a : b;
}
```

**关键演进**：
- C++03：编译器提示（可能被忽略）
- C++17：强制内联变量（单实例保证）
- 模板函数隐式inline（避免ODR违规）

---

### 2. `constexpr`函数

```cpp
// C++11基础版（单一return语句）
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

// C++14增强版（支持循环/局部变量）
constexpr int fibonacci(int n) {
    if (n <= 1) return n;
    int a = 0, b = 1;
    for (int i = 2; i <= n; ++i) {
        int temp = a + b;
        a = b;
        b = temp;
    }
    return b;
}

// C++20扩展（支持虚函数/动态内存）
struct Point {
    constexpr virtual double length() const = 0; // C++20允许虚函数
};

constexpr std::vector<int> build_vec() { // C++20 constexpr容器
    std::vector<int> v;
    v.reserve(5);
    for (int i=0; i<5; ++i) v.push_back(i);
    return v;
}
```

**核心规则**：
- 参数和返回值必须是字面类型
- C++20前禁止动态内存/异常
- 可能同时在编译期和运行时使用

---

### 3. `consteval`函数

```cpp
// 强制编译时求值（C++20）
consteval int cube(int n) {
    return n * n * n;
}

constexpr int val1 = cube(3);  // OK
int runtime_val = 5;
// int val2 = cube(runtime_val); // 错误：参数非编译期常量

// 应用场景：编译期校验
consteval size_t check_size(size_t sz) {
    if (sz > 1024) throw "Size too big"; // 编译期异常
    return sz;
}

char buffer[check_size(2048)]; // 编译失败
```

**特性对比**：

| 特性       | `inline` | `constexpr` | `consteval` |
| ---------- | -------- | ----------- | ----------- |
| 优化级别   | 链接期   | 编译期      | 强制编译期  |
| 可用阶段   | 运行时   | 双重阶段    | 仅编译期    |
| 虚函数支持 | 是       | C++20起     | 否          |

---

## 二、可调用对象

---

### 1. 函数指针

```cpp
// 类型声明语法
using Comparator = bool(*)(int, int); // 别名简化

bool asc(int a, int b) { return a < b; }
bool desc(int a, int b) { return a > b; }

// 回调函数应用
void sort_data(int* arr, size_t len, Comparator comp) {
    std::sort(arr, arr + len, comp);
}

// 使用示例
int main() {
    int data[] = {5, 2, 8, 1};
    sort_data(data, 4, desc); // 降序排序
}
```

**限制**：
- 无法捕获上下文状态
- 不支持重载函数直接转换
- 类型安全较差（无签名检查）

---

### 2. 函数对象（仿函数）

```cpp
class Counter {
    int count = 0;
public:
    void operator()(int x) { // 重载调用运算符
        count += x;
    }
    int get() const { return count; }
};

// 状态保持示例
Counter cnt;
cnt(5);
cnt(3);
std::cout << cnt.get(); // 输出8

// STL算法应用
std::vector<int> nums{1, 2, 3, 4};
int sum = 0;
std::for_each(nums.begin(), nums.end(), [&sum](int n) { sum += n; });
```

**优势**：
- 可维护内部状态
- 支持模板参数推导
- 通常比函数指针更快（易内联优化）

---

### 3. `std::function`类型擦除

```cpp
#include <functional>

void demo_function() {
    // 存储各种可调用对象
    std::function<int(int, int)> func;

    // 绑定普通函数
    func = static_cast<int(*)(int, int)>(&add);

    // 绑定仿函数
    struct Multiplier {
        int factor;
        int operator()(int a, int b) { return a * b * factor; }
    };
    func = Multiplier{2};

    // 绑定Lambda
    func = [](int a, int b) { return a / b; };

    // 调用统一接口
    std::cout << func(6, 3); // 输出2
}
```

**实现原理**：
- 使用虚函数表实现多态
- 小对象优化（SBO）避免堆分配
- 类型擦除带来约~30ns调用开销

---

## 三、Lambda表达式

---

### 1. 捕获列表详解

```cpp
int x = 10, y = 20;

// 值捕获（复制当前值）
auto lambda1 = [x] { return x + 5; };

// 引用捕获（绑定到变量）
auto lambda2 = [&y] { y += 5; };

// 初始化捕获（C++14移动语义）
auto str = std::make_unique<std::string>("Hello");
auto lambda3 = [s = std::move(str)] {
    return s->size();
};

// 混合捕获模式
auto lambda4 = [=, &y](int z) { // 默认值捕获，显式引用y
    return x + y + z;
};
```

**捕获方式**：
- `[]`：无捕获
- `[=]`：隐式值捕获
- `[&]`：隐式引用捕获
- `[var]`：显式值捕获
- `[this]`：捕获当前对象指针

---

### 2. `mutable`关键字

```cpp
int counter = 0;

// 无mutable（值捕获不可修改）
auto lambda1 = [counter]() mutable {
    ++counter; // 修改副本
    return counter;
};
lambda1(); // 返回1
lambda1(); // 返回2
std::cout << counter; // 仍为0

// 引用捕获无需mutable
auto lambda2 = [&counter] {
    ++counter; // 修改原变量
};
```

**关键点**：
- `mutable`允许修改值捕获的副本
- 不影响外部变量
- 使Lambda的`operator()`非const

---

### 3. 闭包类型本质

```cpp
// Lambda表达式
auto lambda = [x=0](int y) mutable {
    return x += y;
};

// 编译器生成等价类
class __Lambda_123 {
    int x;
public:
    __Lambda_123(int x) : x(x) {}
    int operator()(int y) { // 非const因mutable
        return x += y;
    }
};
```

**实现细节**：
- 捕获变量成为成员字段
- `operator()`实现函数体
- 隐式转换为函数指针（当无捕获时）

---

### 4. 泛型Lambda

```cpp
// C++14 auto参数
auto generic_lambda = [](auto x, auto y) {
    return x + y;
};
std::cout << generic_lambda(3, 4.5); // 输出7.5

// C++20模板语法
auto template_lambda = []<typename T>(T x) -> std::type_identity<T> {
    return {x};
};
auto result = template_lambda(42); // result为int类型包装
```

**版本对比**：
| 特性         | C++14  | C++20         |
| ------------ | ------ | ------------- |
| 参数类型声明 | `auto` | 模板语法`<T>` |
| 类型约束     | 无     | 支持概念约束  |
| 返回类型推导 | 自动   | 可显式指定    |
| 嵌套模板     | 不支持 | 支持          |

---

## 关键特性对比表

| 特性       | 函数指针 | 函数对象 | Lambda表达式 | std::function |
| ---------- | -------- | -------- | ------------ | ------------- |
| 状态保持   | ❌        | ✔️        | ✔️            | ✔️             |
| 内联优化   | 困难     | 容易     | 容易         | 困难          |
| 类型安全   | 低       | 高       | 高           | 中            |
| 上下文捕获 | ❌        | ✔️        | ✔️            | ✔️             |
| 语法简洁性 | 中       | 低       | 高           | 高            |
| 运行时开销 | 低       | 低       | 低           | 中            |

---

**最佳实践**：
1. 优先使用Lambda表达式保持代码局部性
2. 需要类型擦除时使用`std::function`，但注意性能影响
3. 编译期计算优先选择`consteval`，次选`constexpr`
4. 需要状态保持的算法使用仿函数
5. 避免在热点路径频繁构造`std::function`
6. C++20后优先使用模板Lambda替代auto参数

```cpp
// 综合示例：编译期分派
template<typename F>
void process_data(F&& func) { // 接受任意可调用对象
    if constexpr (std::is_invocable_r_v<int, F, double>) {
        int res = func(3.14);
        std::cout << "Numeric result: " << res;
    } else {
        func();
        std::cout << "Void function called";
    }
}

// 使用示例
process_data([](double x) { return static_cast<int>(x * 2); });
process_data([] { std::cout << "Hello"; });
```

# 函数模板扩展

---

## 一、模板函数设计

---

### 1. 类型推导规则  
```cpp  
template<typename T>  
void func(T param);  

// 调用示例  
int x = 10;  
const int cx = x;  
const int& rx = x;  
func(x);   // T = int, param = int  
func(cx);  // T = int, param = int（忽略顶层const）  
func(rx);  // T = int, param = int（忽略引用）  
```

**关键机制**：  
- 模板参数推导会忽略引用和顶层`const`  
- 数组/函数类型退化为指针（除非形参是引用类型）  
- 显式指定类型可覆盖推导：`func<int&>(x)`  

---

### 2. SFINAE与`std::enable_if`  
```cpp  
// 通过返回类型启用模板  
template<typename T>  
typename std::enable_if<std::is_integral<T>::value, void>::type  
process(T val) { /* 处理整型 */ }  

template<typename T>  
typename std::enable_if<std::is_floating_point<T>::value, void>::type  
process(T val) { /* 处理浮点型 */ }  

// C++20概念替代方案  
template<typename T>  
requires std::integral<T>  
void modern_process(T val) { /* 仅接受整型 */ }  
```

**SFINAE原理**：  
- 替换失败不会导致编译错误，而是从重载集中剔除  
- 常用于类型特征检查或表达式有效性验证  

---

### 3. C++20概念约束  
```cpp  
template<typename T>  
concept Addable = requires(T a, T b) {  
    { a + b } -> std::convertible_to<T>; // 要求可相加且结果可转换  
};  

template<Addable T>  
T sum(T a, T b) { return a + b; }  

// 复合约束  
template<typename T>  
requires Addable<T> && std::default_initializable<T>  
class Container { /*...*/ };  
```

**优势**：  
- 更清晰的错误信息  
- 替代复杂的SFINAE技巧  
- 支持布尔逻辑组合约束（`&&`/`||`）  

---

## 二、变参模板与转发

---

### 1. 参数包展开  
```cpp  
// 递归终止条件  
void print() {}  

template<typename T, typename... Args>  
void print(T first, Args... args) {  
    std::cout << first << " ";  
    print(args...);  
}  

// C++17折叠表达式  
template<typename... Args>  
auto sum(Args... args) {  
    return (... + args); // (((arg1 + arg2) + arg3) + ...)  
}  
```

**展开方式**：  
- 递归展开：需要终止条件和参数剥离  
- 折叠表达式：支持四类操作符（`+`, `*`, `&`, `|`）  

---

### 2. 完美转发  
```cpp  
template<typename... Args>  
void relay(Args&&... args) {  
    target(std::forward<Args>(args)...);  
}  

// 万能引用+转发保留值类别  
template<typename T>  
void wrap(T&& arg) {  
    process(std::forward<T>(arg));  
}  
```

**转发规则**：  
- `std::forward`根据引用折叠规则保留值类别（左值/右值）  
- 参数包展开时需逐个转发  

---

### 3. 变参模板与`std::tuple`  
```cpp  
template<typename... Args>  
auto make_tuple(Args&&... args) {  
    return std::tuple<Args...>(std::forward<Args>(args)...);  
}  

template<size_t... Is, typename Tuple>  
void print_tuple_impl(Tuple&& t, std::index_sequence<Is...>) {  
    (..., (std::cout << std::get<Is>(t) << " "));  
}  

template<typename... Args>  
void print_tuple(const std::tuple<Args...>& t) {  
    print_tuple_impl(t, std::index_sequence_for<Args...>{});  
}  
```

**交互技巧**：  
- 使用`std::index_sequence`生成编译时索引序列  
- 折叠表达式简化元组遍历  

---

## 三、模板元编程

---

### 1. 编译时条件判断  
```cpp  
template<typename T>  
auto get_value(T t) {  
    if constexpr (std::is_pointer_v<T>) {  
        return *t;  
    } else {  
        return t;  
    }  
}  

// 替代模板特化方案  
template<typename T>  
void process() {  
    if constexpr (std::is_integral_v<T>) {  
        // 整型处理逻辑  
    } else if constexpr (std::is_floating_point_v<T>) {  
        // 浮点处理逻辑  
    }  
}  
```

**优势**：  
- 简化多重特化代码  
- 支持更复杂的条件逻辑  

---

### 2. 类型萃取与转换  
```cpp  
template<typename T>  
struct remove_all_pointers {  
    using type = T;  
};  

template<typename T>  
struct remove_all_pointers<T*> {  
    using type = typename remove_all_pointers<T>::type;  
};  

// 使用类型特征  
using NakedType = remove_all_pointers<int****>::type; // int  
```

**标准库工具**：  
- `std::decay`：模拟传值类型转换  
- `std::conditional`：类型条件选择  

---

## 关键机制对比表

| 技术           | 典型应用场景     | 优势                   | 局限               |
| -------------- | ---------------- | ---------------------- | ------------------ |
| SFINAE         | 条件启用模板重载 | 兼容旧标准             | 代码冗余，可读性差 |
| C++20概念      | 模板参数约束     | 清晰语义，更好错误提示 | 需要C++20支持      |
| 折叠表达式     | 参数包聚合操作   | 简化递归代码           | 仅支持有限操作符   |
| `if constexpr` | 编译时分支       | 替代部分模板特化       | 不能用于运行时条件 |
| 类型萃取       | 元编程类型操作   | 编译时类型操作         | 需要模板特化知识   |

---

## 最佳实践

1. **优先使用C++20概念**：  
   ```cpp  
   template<std::semiregular T>  // 标准库概念  
   class Container { /*...*/ };  
   ```

2. **完美转发规范**：  
   ```cpp  
   template<typename... Args>  
   void emplace_wrapper(Args&&... args) {  
       data.emplace(std::forward<Args>(args)...);  
   }  
   ```

3. **编译时静态检查**：  
   ```cpp  
   template<typename T>  
   void safe_op(T val) {  
       static_assert(std::is_arithmetic_v<T>, "Requires numeric type");  
       // ...  
   }  
   ```

4. **变参模板调试**：  
   ```cpp  
   template<typename... Args>  
   void debug_print(Args&&... args) {  
       (std::clog << ... << args) << '\n'; // C++17折叠表达式  
   }  
   ```

5. **元编程优化**：  
   ```cpp  
   template<auto N>  // C++17非类型模板参数推导  
   constexpr auto factorial() {  
       if constexpr (N <= 1) return 1;  
       else return N * factorial<N - 1>();  
   }  
   ```

---

```cpp  
// 综合示例：类型安全格式化函数  
template<typename... Args>  
std::string format(const std::string& fmt, Args&&... args) {  
    static_assert((std::is_constructible_v<std::string_view, Args> && ...),  
                  "All arguments must be convertible to string_view");  

    std::ostringstream oss;  
    size_t last = 0;  
    ([&](const auto& arg) {  
        size_t pos = fmt.find("{}", last);  
        if(pos == std::string::npos) return;  
        oss << fmt.substr(last, pos - last) << arg;  
        last = pos + 2;  
    } (args), ...);  
    oss << fmt.substr(last);  
    return oss.str();  
} 
```

---

## 一、设计范式

---

### 1. 单一职责原则（SRP）

```cpp
// 违反SRP的反例
void process_data(std::vector<int>& data) {
    // 职责1：数据清洗
    data.erase(std::remove_if(data.begin(), data.end(), 
        [](int x){ return x < 0; }), data.end());
    
    // 职责2：数据计算
    int sum = std::accumulate(data.begin(), data.end(), 0);
    
    // 职责3：结果输出
    std::cout << "Result: " << sum << "\n";
}

// 遵循SRP的正例
void clean_data(std::vector<int>& data) {
    data.erase(std::remove_if(data.begin(), data.end(), 
        [](int x){ return x < 0; }), data.end());
}

int calculate_sum(const std::vector<int>& data) {
    return std::accumulate(data.begin(), data.end(), 0);
}

void output_result(int result) {
    std::cout << "Result: " << result << "\n";
}
```

**关键点**：
- 每个函数完成独立原子操作
- 函数复用率提高50%-70%
- 测试用例更容易编写

---

### 2. 最小惊讶原则（POLA）

```cpp
// 违反POLA的参数顺序
void configure_device(int baud_rate, bool parity_check, int stop_bits) {
    /* 参数顺序不符合行业标准 */
}

// 遵循POLA的改进
struct SerialConfig {
    int baud_rate = 9600;
    int stop_bits = 1;
    bool parity = false;
};

void configure_device(const SerialConfig& cfg) {
    /* 使用结构体封装参数 */
}

// 符合直觉的API设计示例
class FileSystem {
public:
    // 参数顺序：源路径 -> 目标路径
    void copy_file(const Path& src, const Path& dest); 
};
```

**常见违反场景**：
- 布尔参数缺乏自解释性
- 混合输入/输出参数
- 非常规单位参数（角度用弧度还是度数）

---

### 3. KISS原则

```cpp
// 过度泛化的模板
template<typename T, typename Transformer, typename Validator>
auto process_data(T input, Transformer trans, Validator valid) {
    if (!valid(input)) throw std::invalid_argument("...");
    return trans(input);
}

// 简化后的版本
double calculate_bmi(double weight_kg, double height_m) {
    constexpr double MIN_HEIGHT = 0.5;
    if (height_m < MIN_HEIGHT) throw std::domain_error("...");
    return weight_kg / (height_m * height_m);
}
```

**复杂度控制指标**：
| 指标     | 建议阈值 |
| -------- | -------- |
| 函数行数 | ≤ 25行   |
| 嵌套层次 | ≤ 3层    |
| 参数数量 | ≤ 4个    |
| 圈复杂度 | ≤ 10     |

---

## 二、错误处理机制

---

### 1. 异常安全等级

```cpp
class DatabaseConnection {
    std::unique_ptr<Connection> conn;
public:
    // 强异常安全保证
    void update_record(int id, const std::string& value) {
        auto new_conn = std::make_unique<Connection>(*conn); // Copy
        new_conn->execute("UPDATE...");
        std::swap(conn, new_conn); // Commit点
    }

    // 不抛异常保证
    void close() noexcept {
        if (conn) conn->release();
    }
};
```

**安全等级对比**：
| 等级     | 描述                   | 实现成本 |
| -------- | ---------------------- | -------- |
| 基本保证 | 不泄露资源，对象仍有效 | 低       |
| 强保证   | 操作全成功或全失败     | 中       |
| 不抛异常 | 确保绝不抛出异常       | 高       |

---

### 2. 异常中立性

```cpp
// 错误处理反例：吞噬异常
void process_file() {
    try {
        File f = open("data.bin");
        // ...
    } catch (...) {} // 危险！隐藏错误
}

// 正确的中立处理
void log_and_rethrow() {
    try {
        // ... 业务代码 ...
    } catch (const std::exception& e) {
        log_error(e.what());
        throw; // 保留原始异常信息
    }
}
```

**最佳实践**：
- 仅在能处理异常时捕获
- 资源管理使用RAII模式
- 顶层模块统一处理未捕获异常

---

### 3. `noexcept`优化

```cpp
class MovableResource {
    int* data;
public:
    // 移动操作声明noexcept
    MovableResource(MovableResource&& other) noexcept 
        : data(std::exchange(other.data, nullptr)) {}
    
    ~MovableResource() noexcept {
        delete[] data;
    }
};

// 影响容器行为
static_assert(std::is_nothrow_move_constructible_v<MovableResource>);
```

**需标注`noexcept`的场景**：
- 移动构造函数
- 移动赋值运算符
- 交换操作
- 析构函数（C++11起默认）

---

### 4. 错误码设计

```cpp
enum class NetworkError {
    Success = 0,
    Timeout,
    ConnectionRefused,
    ProtocolError
};

std::error_code make_error(NetworkError e) {
    static const std::error_category& net_category = ...;
    return {static_cast<int>(e), net_category};
}

bool send_packet(Packet p, std::error_code& ec) {
    if (/* 超时 */) {
        ec = make_error(NetworkError::Timeout);
        return false;
    }
    // ...
    return true;
}
```

**错误码vs异常**：
| 维度     | 错误码         | 异常         |
| -------- | -------------- | ------------ |
| 性能影响 | 无额外开销     | 栈展开成本   |
| 错误追溯 | 需要手动传递   | 自动传播     |
| 适用场景 | 频繁可恢复错误 | 重大意外错误 |

---

## 三、接口契约

---

### 1. 前置条件

```cpp
void draw_rectangle(int width, int height) {
    // C标准断言
    assert(width > 0 && height > 0);
    
    // GSL风格
    Expects(width < MAX_SIZE && height < MAX_SIZE);
    
    // 业务逻辑...
}

// 编译期检查（C++20）
template<typename T>
concept Positive = requires(T v) { requires v > 0; };

void process(Positive auto value) { /*...*/ }
```

**验证方式对比**：
| 方法     | 检查时机 | 生产环境影响 |
| -------- | -------- | ------------ |
| assert   | 调试阶段 | 可禁用       |
| Expects  | 始终检查 | 可能崩溃     |
| 概念约束 | 编译期   | 无运行时开销 |

---

### 2. 后置条件

```cpp
// C++ Contracts提案语法（未正式通过）
int safe_divide(int a, int b) 
    [[pre: b != 0]]
    [[post result: result * b == a]] 
{
    return a / b;
}

// 当前实现方案
int legacy_divide(int a, int b) {
    int result = a / b;
    assert(result * b == a); // 后置条件检查
    return result;
}
```

**设计要点**：
- 避免影响函数核心逻辑
- 检查结果的有效性而非实现过程
- 与单元测试结合验证

---

### 3. `[[nodiscard]]`属性

```cpp
// 必须检查返回值
[[nodiscard]] void* allocate(size_t size) {
    return ::malloc(size);
}

// 带说明信息（C++20）
[[nodiscard("必须检查连接状态")]] 
bool connect_to_server() {
    return /* 连接结果 */;
}

// 枚举类型级联标记
enum [[nodiscard]] ErrorCode { OK, WARNING, CRITICAL };
```

**适用场景**：
- 资源获取函数
- 可能失败的初始化操作
- 状态查询方法

---

## 四、性能与可维护性

---

### 1. 避免过度内联

```cpp
// 过度内联导致代码膨胀
class VectorProcessor {
public:
    inline void process(int* data) { /* 复杂运算 */ }
    inline void validate() { /* 大量校验逻辑 */ }
    inline void save_result() { /* 文件操作 */ }
};

// 合理的内联策略
class OptimizedMath {
public:
    __attribute__((always_inline)) 
    static double fast_sin(double x) { /* 短小数学函数 */ }
};
```

**内联决策矩阵**：
| 函数大小 | 调用频率 | 建议决策   |
| -------- | -------- | ---------- |
| <5行     | 高频     | 强制内联   |
| 5-20行   | 中频     | 编译器决定 |
| >20行    | 低频     | 禁止内联   |

---

### 2. 参数传递优化

```cpp
// 小对象传值（sizeof <= 2*指针）
void process_point(Point pt); // Point {x,y}

// 大对象传引用
void analyze_image(const Image& img);

// 输出参数使用指针
bool parse_input(const std::string& s, int* out_val);

// 完美转发模板
template<typename T>
void sink_data(T&& data) {
    storage_.push_back(std::forward<T>(data));
}
```

**传递方式指南**：
| 类型         | 大小限制 | 推荐方式      |
| ------------ | -------- | ------------- |
| 基本类型     | ≤8字节   | 值传递        |
| 只读对象     | 任意     | const&        |
| 需要修改的   | -        | 非const引用   |
| 移动语义对象 | 大对象   | 值传递 + 移动 |

---

### 3. 文档化规范

```cpp
/**
 * @brief 计算两个向量的点积
 * 
 * @param v1 第一个向量，长度必须大于等于3
 * @param v2 第二个向量，必须与v1等长
 * @return double 计算结果
 * @throws std::invalid_argument 当向量长度不匹配时抛出
 * @pre v1.size() == v2.size()
 * @post 返回值范围在[-1, 1]之间
 */
[[nodiscard]] 
double dot_product(const std::vector<double>& v1, 
                  const std::vector<double>& v2);
```

**文档要素**：
- 功能简述
- 参数约束
- 返回值说明
- 异常行为
- 复杂度分析
- 算法来源

---

## 关键原则对照表

| 原则       | 优点           | 典型应用场景     |
| ---------- | -------------- | ---------------- |
| SRP        | 提高可维护性   | 复杂业务逻辑分解 |
| POLA       | 降低使用成本   | API设计          |
| KISS       | 减少维护成本   | 基础工具函数     |
| 强异常安全 | 保证数据一致性 | 事务操作         |
| 契约设计   | 提前暴露问题   | 公共库接口       |
| 参数优化   | 提升运行时性能 | 高频调用函数     |

---

**综合实践建议**：
1. 核心模块采用契约设计+异常中立
2. 高频小函数使用内联+值传递
3. 跨模块接口使用错误码+详细文档
4. 资源管理类保证强异常安全
5. 模板库优先使用概念约束
6. 保持函数圈复杂度低于10

```cpp
// 综合示例：安全数据处理器
class DataProcessor {
public:
    /**
     * @brief 处理输入数据并返回加密结果
     * @param input 需要处理的原始数据，长度必须为256位
     * @param key 加密密钥，必须已初始化
     * @return [[nodiscard]] 加密结果数据块
     * @throws CryptoException 加密失败时抛出
     */
    [[nodiscard]]
    CryptoBlock process(InputData input, const CryptoKey& key) {
        Expects(input.size() == BLOCK_SIZE);
        if (!key.valid()) throw CryptoException("Invalid key");
        
        auto intermediate = preprocess(input);
        CryptoBlock result = encrypt(intermediate, key);
        
        Ensures(result.verify());
        return result;
    }

private:
    // 内部实现细节...
};
```


# 扩展与实战

---

## 一、现代C++新特性

---

### 1. C++20协程

```cpp
#include <coroutine>
#include <iostream>

// 生成器协程实现
generator<int> fibonacci_seq() {
    int a = 0, b = 1;
    while (true) {
        co_yield a; // 挂起并返回值
        std::tie(a, b) = std::make_tuple(b, a + b);
    }
}

// 使用示例
int main() {
    auto gen = fibonacci_seq();
    for (int i = 0; i < 10; ++i) {
        std::cout << gen.resume() << " "; // 输出前10个斐波那契数
    }
}
```

**关键特性**：
- `co_await`：挂起当前协程
- `co_yield`：产生中间结果
- 协程帧自动内存管理
- 需要编译器支持（MSVC/Clang实验性支持）

---

### 2. Ranges函数式编程

```cpp
#include <ranges>
#include <vector>
#include <algorithm>

void process_data() {
    std::vector nums{3, 1, 4, 1, 5, 9, 2, 6};
    
    auto result = nums 
        | std::views::filter([](int x) { return x % 2 == 0; })  // 过滤偶数
        | std::views::transform([](int x) { return x * 2; })   // 加倍
        | std::views::take(3);                                  // 取前3个
    
    // 输出结果：8 4 12
    std::ranges::for_each(result, [](int x) { std::cout << x << " "; });
}
```

**核心优势**：
- 惰性求值优化性能
- 管道操作符`|`提升可读性
- 支持无限序列处理
- 与STL算法无缝集成

---

## 二、设计模式应用

---

### 1. 策略模式

```cpp
class SortStrategy {
public:
    virtual void sort(std::vector<int>& data) = 0;
    virtual ~SortStrategy() = default;
};

class QuickSort : public SortStrategy {
    void sort(std::vector<int>& data) override {
        std::sort(data.begin(), data.end());
    }
};

class MergeSort : public SortStrategy {
    void sort(std::vector<int>& data) override {
        std::stable_sort(data.begin(), data.end());
    }
};

// 使用函数对象实现策略
class DataProcessor {
    std::function<void(std::vector<int>&)> strategy_;
public:
    explicit DataProcessor(auto strategy) : strategy_(strategy) {}
    
    void process(std::vector<int>& data) {
        strategy_(data);
    }
};

// 使用示例
DataProcessor processor1{[](auto& data) { std::sort(data.begin(), data.end()); }};
DataProcessor processor2{[](auto& data) { std::ranges::sort(data); }};
```

---

### 2. 工厂函数

```cpp
#include <memory>

class ILogger {
public:
    virtual void log(const std::string& msg) = 0;
    virtual ~ILogger() = default;
};

std::unique_ptr<ILogger> create_logger(const std::string& type) {
    if (type == "file") 
        return std::make_unique<FileLogger>();
    if (type == "console")
        return std::make_unique<ConsoleLogger>();
    throw std::invalid_argument("Unknown logger type");
}

// 现代C++工厂模板
template<typename T, typename... Args>
auto make(Args&&... args) {
    return std::make_unique<T>(std::forward<Args>(args)...);
}
```

**工厂模式优势**：
- 对象创建与使用解耦
- 支持多态实例化
- 资源管理自动化（配合智能指针）

---

## 三、调试与优化

---

### 1. 函数调用栈分析

```bash
# GDB回溯调用栈
(gdb) break my_function
(gdb) run
(gdb) backtrace

# 性能分析工具使用
$ perf record -g ./my_program
$ perf report -g graph,callee

# 可视化火焰图生成
$ perf script | stackcollapse-perf.pl | flamegraph.pl > flame.svg
```

**调试技巧**：
- 使用`-fno-omit-frame-pointer`保留帧指针
- `-Og`优化等级保留调试信息
- 结合Valgrind检测内存问题

---

### 2. 内联函数调试

```cpp
// 显式禁用内联
__attribute__((noinline)) 
void critical_function(int x) {
    // 复杂逻辑...
}

// 编译选项控制
$ g++ -fno-inline -o my_prog main.cpp

// 调试宏定义
#ifdef DEBUG
#define INLINE 
#else
#define INLINE inline __attribute__((always_inline))
#endif
```

**内联管理策略**：
| 场景         | 推荐操作       |
| ------------ | -------------- |
| 调试关键路径 | 禁用内联       |
| 性能敏感函数 | 强制内联       |
| 模板函数     | 依赖编译器决策 |

---

## 四、跨语言交互

---

### 1. C/C++互操作

```cpp
// C++导出接口
extern "C" {
    __declspec(dllexport) 
    int calculate(int a, int b) {
        return a + b;
    }
}

// C封装层示例
#ifdef __cplusplus
extern "C" {
#endif

typedef void* Handle;
Handle create_processor();
void process_data(Handle h, const char* input);
void release_processor(Handle h);

#ifdef __cplusplus
}
#endif
```

**接口设计原则**：
- 使用基本类型（int/double/指针）
- 避免抛出异常（使用错误码）
- 显式资源管理接口
- 版本兼容性处理

---

### 2. Python扩展开发

```cpp
// pybind11示例
#include <pybind11/pybind11.h>

namespace py = pybind11;

int add(int a, int b) {
    return a + b;
}

PYBIND11_MODULE(example, m) {
    m.def("add", &add, "A function to add two numbers");
}

// 编译命令
$ c++ -O3 -Wall -shared -std=c++11 -fPIC $(python3 -m pybind11 --includes) example.cpp -o example$(python3-config --extension-suffix)
```

**跨语言交互对比**：
| 技术     | 调用开销 | 类型安全 | 开发效率 |
| -------- | -------- | -------- | -------- |
| C接口    | 低       | 差       | 低       |
| pybind11 | 中       | 高       | 高       |
| SWIG     | 高       | 中       | 中       |

---

## 实战案例：高性能日志系统

```cpp
// 协程+策略模式+RAII
class AsyncLogger {
    std::jthread worker_;
    moodycamel::ConcurrentQueue<std::string> queue_;
    
public:
    AsyncLogger() : worker_([this](std::stop_token st) {
        while (!st.stop_requested()) {
            std::string msg;
            if (queue_.try_dequeue(msg)) {
                write_to_disk(msg); // 实际写入操作
            }
            std::this_thread::yield();
        }
    }) {}

    ~AsyncLogger() { worker_.request_stop(); }

    template<typename... Args>
    void log(Args&&... args) {
        queue_.enqueue(std::format(std::forward<Args>(args)...));
    }
};

// 使用示例
AsyncLogger logger;
logger.log("User {} login at {}", "admin", std::chrono::system_clock::now());
```

**系统特性**：
- 无锁队列保证线程安全
- 协程优化资源利用率
- RAII自动管理生命周期
- 格式化参数类型安全

---

## 跨平台开发注意事项

| 平台差异     | 解决方案              |
| ------------ | --------------------- |
| ABI兼容性    | 使用标准C类型接口     |
| 异常处理机制 | 边界处转换为错误码    |
| 内存对齐     | 使用alignas关键字     |
| 线程模型     | 统一使用std::thread   |
| 调试符号     | 保留PDB文件/DWARF信息 |

---

**终极实践建议**：
1. 核心算法使用现代C++特性实现
2. 跨语言接口保持极简设计
3. 性能关键路径结合协程与无锁结构
4. 模块边界采用策略模式增强扩展性
5. 调试版本保留完整符号信息
6. 通过CI保证跨平台兼容性

```cpp
// 综合应用：协程网络服务
task<void> handle_connection(Socket sock) {
    try {
        auto data = co_await sock.async_read();
        auto result = process_request(data);
        co_await sock.async_write(result);
    } catch (const network_error& e) {
        log_error(e.what());
    }
}

// 启动服务协程
io_context.run([&] {
    while (true) {
        auto sock = co_await accept_async();
        handle_connection(std::move(sock));
    }
});
```
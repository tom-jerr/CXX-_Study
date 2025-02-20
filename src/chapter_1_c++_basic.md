# Chapter 1 C++ Basic
```admonish info
A beautifully styled message.
```


```admonish warning
A beautifully styled message.
```

```admonish danger
A beautifully styled message.
```

```admonish example
A beautifully styled message.
```

```admonish
A plain note.
```

## 1 Container

### 1.1 std::vector




## 2 Algorithm
### 2.1 std::transform
- std::transform applies the given function to the elements of the given input range(s), and stores the result in an output range starting from d_first.
#### parameters

- first1, last1	-	the pair of iterators defining the source range of elements to transform  
- first2	-	the beginning of the second range of elements to transform, (3,4) only
- d_first	-	the beginning of the destination range, may be equal to first1 or first2
- policy	-	the execution policy to use
- unary_op	-	Ret fun(const Type &a);
- binary_op	-	Ret fun(const Type1 &a, const Type2 &b);


#### possible implementation

``` c++
template<class InputIt, class OutputIt, class UnaryOp>
constexpr //< since C++20
OutputIt transform(InputIt first1, InputIt last1,
                   OutputIt d_first, UnaryOp unary_op)
{
    for (; first1 != last1; ++d_first, ++first1)
        *d_first = unary_op(*first1);
 
    return d_first;
}

template<class InputIt1, class InputIt2, 
         class OutputIt, class BinaryOp>
constexpr //< since C++20
OutputIt transform(InputIt1 first1, InputIt1 last1, InputIt2 first2,
                   OutputIt d_first, BinaryOp binary_op)
{
    for (; first1 != last1; ++d_first, ++first1, ++first2)
        *d_first = binary_op(*first1, *first2);
 
    return d_first;
}
```
#### example

```c++
#include <algorithm>
#include <cctype>
#include <iomanip>
#include <iostream>
#include <string>
#include <utility>
#include <vector>
 
void print_ordinals(const std::vector<unsigned>& ordinals)
{
    std::cout << "ordinals: ";
    for (unsigned ord : ordinals)
        std::cout << std::setw(3) << ord << ' ';
    std::cout << '\n';
}
 
char to_uppercase(unsigned char c)
{
    return std::toupper(c);
}
 
void to_uppercase_inplace(char& c)
{
    c = to_uppercase(c);
}
 
void unary_transform_example(std::string& hello, std::string world)
{
    // Transform string to uppercase in-place
 
    std::transform(hello.cbegin(), hello.cend(), hello.begin(), to_uppercase);
    std::cout << "hello = " << std::quoted(hello) << '\n';
 
    // for_each version (see Notes above)
    std::for_each(world.begin(), world.end(), to_uppercase_inplace);
    std::cout << "world = " << std::quoted(world) << '\n';
}
 
void binary_transform_example(std::vector<unsigned> ordinals)
{
    // Transform numbers to doubled values
 
    print_ordinals(ordinals);
 
    std::transform(ordinals.cbegin(), ordinals.cend(), ordinals.cbegin(),
                   ordinals.begin(), std::plus<>{});
 
    print_ordinals(ordinals);
}
 
int main()
{
    std::string hello("hello");
    unary_transform_example(hello, "world");
 
    std::vector<unsigned> ordinals;
    std::copy(hello.cbegin(), hello.cend(), std::back_inserter(ordinals));
    binary_transform_example(std::move(ordinals));
}
// OUTPUT
// hello = "HELLO"
// world = "WORLD"
// ordinals:  72  69  76  76  79 
// ordinals: 144 138 152 152 158
```
### 2.2 std::accumulate
- Computes the sum of the given value init and the elements in the range [first, last).

#### parameters


- first, last	-	the pair of iterators defining the range of elements to accumulate
- init	-	initial value of the accumulate
- op	-	Ret fun(const Type1 &a, const Type2 &b);
  > 如标准库中的std::plus<>


#### possible implementation

```c++

accumulate (1)
template<class InputIt, class T>
constexpr // since C++20
T accumulate(InputIt first, InputIt last, T init)
{
    for (; first != last; ++first)
        init = std::move(init) + *first; // std::move since C++20
 
    return init;
}

template<class InputIt, class T, class BinaryOperation>
constexpr // since C++20
T accumulate(InputIt first, InputIt last, T init, BinaryOperation op)
{
    for (; first != last; ++first)
        init = op(std::move(init), *first); // std::move since C++20
 
    return init;
}
```
### 2.3 std::optinal
- std::optional最高效的写法是触发RVO的写法，即：

    ```c++
    optional<A> optional_best(int n) {
        optional<A> temp(someFn(n));
        return temp;
    }
    
    ```

## 3 Iterator
- 输入迭代器：只能用来读取指向的值；当该迭代器自加时，之前指向的值就不可访问。
  > std::istream_iterator 就是这样的迭代器。
- 前向迭代器：类似于输入迭代器，可以在指示范围迭代多次
  > std::forward_list 就是这样的迭代器。就像一个单向链表一样，只能向前遍历，不能向后遍历，但可以反复迭代。 
- 双向迭代器：这个迭代器可以自增，也可以自减，迭代器可以向前或向后迭代。
  > std::list, std::set 和 std::map 都支持双向迭代器。
- 随机访问迭代器：与其他迭代器不同，随机访问迭代器一次可以跳转到任何容器中的元素上，而非之前的迭代器，一次只能移动一格。 
  > std::vector 和 std::deque 的迭代器就是这种类型。
- 连续迭代器：这种迭代器具有前述几种迭代器的所有特性，不过需要容器内容在内存上是连续的，类似一个数组或 std::vector 。
- 输出迭代器：该迭代器与其他迭代器不同。因为这是一个单纯用于写出的迭代器，其只能增加，并且将对应内容写入文件当中。如果要读取这个迭代中的数据，那么读取到的值就是未定义的
- 可变迭代器：如果一个迭代器既有输出迭代器的特性，又有其他迭代器的特性，那么这个迭代器就是可变迭代器。
  > 该迭代器可读可写。如果我们从一个非常量容器的实例中获取一个迭代器，那么这个迭代器通常都是可变迭代器。
### 3.1 std::back_insert_iterator
- 内部调用容器的`push_back`方法

    ```c++
    // 使用 back_insert_iterator 在 destination 的末尾插入元素
    std::copy(source.begin(), source.end(), std::back_inserter(destination));
    ```
### 3.2 std::front_insert_iterator
- 内部调用容器的`push_front`方法
  ```c++
  // 使用 front_insert_iterator 在 destination 的开头插入元素
  std::copy(source.begin(), source.end(), std::front_inserter(destination));  
  ```
### 3.3 std::insert_iterator
- std::insert_iterator 是一个通用的插入迭代器，可以在容器的任意位置插入元素。它需要一个容器和一个插入位置。
  ```c++
  // 在 destination 的第二个位置插入 source 的元素
  std::copy(source.begin(), source.end(), std::inserter(destination, destination.begin() + 1));
  ```

### 3.4 std::istream_iterator
- std::istream_iterator 是一个输入流迭代器，用于从输入流（如 std::cin）读取数据
```c++
#include <iostream>
#include <vector>
#include <iterator>
#include <algorithm> // std::copy

int main() {
    std::vector<int> numbers;

    std::cout << "Enter some integers (end with EOF):" << std::endl;

    // 使用 istream_iterator 从 std::cin 读取整数
    std::copy(std::istream_iterator<int>(std::cin), std::istream_iterator<int>(), std::back_inserter(numbers));

    std::cout << "You entered:" << std::endl;
    for (int num : numbers) {
        std::cout << num << " ";
    }
    // 输出用户输入的整数
    return 0;
}
```
### 3.5 std::ostream_iterator
- std::ostream_iterator 是一个输出流迭代器，用于将数据写入输出流（如 std::cout）
```c++
#include <iostream>
#include <vector>
#include <iterator>
#include <algorithm> // std::copy

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};

    // 使用 ostream_iterator 将 numbers 写入 std::cout，每个元素后加空格
    std::copy(numbers.begin(), numbers.end(), std::ostream_iterator<int>(std::cout, " "));

    // 输出: 1 2 3 4 5
    return 0;
}
```





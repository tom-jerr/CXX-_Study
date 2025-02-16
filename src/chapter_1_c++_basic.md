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










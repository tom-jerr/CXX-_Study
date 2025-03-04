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
- 迭代器在vector插入超过容量后，自动扩容时失效
  > 引用语义在使用 span 时必须谨慎。将一个新元素插入 vector 中,该 vector 中保存了跨度所引用的元素。由于 span 的引用语义,若 vector 分配新的内存，会使所有迭代器和指向其元素的指针无效,所以重新分配也会使引用 vector 元素的 span 失效。span 指向了不再存在的元素。  出于这个原因,需要在插入前后都要仔细检查容量 (分配内存的最大元素数量)。若容量发生变化,则重新初始化 span



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
### 2.4 std::move
- 将 `[first, last)` 范围内的元素移动到以 d_first 开始的目标范围
```c++
template <typename InputIt, typename OutputIt>
OutputIt std::move(InputIt first, InputIt last, OutputIt d_first);
```
- 返回一个迭代器，指向目标范围移动后的结束位置（即 d_first + (last - first)）。
  
|参数|	类型|	描述|
|---|---|---|
|first|	InputIt|	源范围的起始迭代器（指向要移动的第一个元素）|
|last|	InputIt|	源范围的结束迭代器（指向最后一个元素的下一个位置）|
|d_first|	OutputIt|	目标范围的起始迭代器（指向移动后第一个元素位置）|

### 2.5 std::move_backward
- 目标位置：元素会被移动到以 d_last 为结束的目标范围，即目标范围是 `[d_last - N, d_last)`，其中 N = last - first。
```c++
template <class BidirIt1, class BidirIt2>
BidirIt2 std::move_backward(BidirIt1 first, BidirIt1 last, BidirIt2 d_last);
```
- 返回一个迭代器，指向目标范围移动后的起始位置（即 d_last - (last - first)）

|参数|	类型|	描述|
|---|---|---|
|first|	BidirIt1|	源范围的起始迭代器（指向要移动的第一个元素）|
|last|	BidirIt1|	源范围的结束迭代器（指向最后一个元素的下一个位置）|
|d_last|	BidirIt2|	目标范围的结束迭代器（指向移动后最后一个元素的下一个位置）|

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
## 4 FileSystem
- 文件系统库提供对文件系统及其组件（例如路径、常规文件和目录）执行操作的工具。
    >文件系统库最初开发为boost.filesystem ，并作为技术规范 ISO/IEC TS 18822:2015发布，最终于 C++17 合并到 ISO C++ 中。目前，boost 实现在比 C++17 库更多的编译器和平台上可用。

- 如果对此库中的函数的调用引发文件系统竞争，即当**多个线程、进程或计算机交错访问和修改文件系统中的同一对象**时，则行为未定义。

### 4.1 定义
- file：保存数据的文件系统对象，可以写入、读取或两者兼而有之。文件具有名称、属性，其中之一是文件类型：
    - 目录：充当目录条目容器的文件，用于标识其他文件（其中一些可能是其他嵌套目录）。在讨论特定文件时，该文件作为条目出现的目录是其父目录。父目录可以用相对路径名表示“……”。
    - 常规文件：将名称与现有文件关联的目录条目（即硬链接）。如果支持多个硬链接，则在删除指向该文件的最后一个硬链接后，该文件将被删除。
    - 符号链接：将名称与路径相关联的目录条目，该路径可能存在也可能不存在。
    - 其他特殊文件类型：块、字符、fifo、套接字。
- 文件名：用于命名文件的字符串。允许的字符、区分大小写、最大长度和不允许的名称由实现定义。名称“.”和“..”在库层面具有特殊含义。
- 路径：标识文件的元素序列。它以可选的根名称开头​​（例如“C：”或者“//server”在 Windows 上），然后是可选的根目录（例如“/”在 Unix 上），后跟零个或多个文件名序列（除最后一个文件名外，其他文件名都必须是目录或目录链接）。路径 (pathname )的字符串表示的本机格式（例如，使用哪些字符作为分隔符）和字符编码是实现定义的，此库提供可移植的路径表示。
    - 绝对路径：明确标识文件位置的路径。
    - 规范路径：不包含符号链接的绝对路径，“.”或者“..”元素。
    - 相对路径：用于标识文件相对于文件系统上某个位置的位置的路径。特殊路径名“.”（点，“当前目录”）和“..”（点点，“父目录”）是相对路径。

### 4.2 directory

```c++
#include <algorithm>
#include <filesystem>
#include <fstream>
#include <iostream>
 
int main()
{
    const std::filesystem::path sandbox{"sandbox"};
    std::filesystem::create_directories(sandbox/"dir1"/"dir2");
    std::ofstream{sandbox/"file1.txt"};
    std::ofstream{sandbox/"file2.txt"};
 
    std::cout << "directory_iterator:\n";
    // directory_iterator can be iterated using a range-for loop
    for (auto const& dir_entry : std::filesystem::directory_iterator{sandbox}) 
        std::cout << dir_entry.path() << '\n';
 
    std::cout << "\ndirectory_iterator as a range:\n";
    // directory_iterator behaves as a range in other ways, too
    std::ranges::for_each(
        std::filesystem::directory_iterator{sandbox},
        [](const auto& dir_entry) { std::cout << dir_entry << '\n'; });
 
    std::cout << "\nrecursive_directory_iterator:\n";
    for (auto const& dir_entry : std::filesystem::recursive_directory_iterator{sandbox}) 
        std::cout << dir_entry << '\n';
 
    // delete the sandbox dir and all contents within it, including subdirs
    std::filesystem::remove_all(sandbox);
}
// Possible output:
// directory_iterator:
// "sandbox/file2.txt"
// "sandbox/file1.txt"
// "sandbox/dir1"
 
// directory_iterator as a range:
// "sandbox/file2.txt"
// "sandbox/file1.txt"
// "sandbox/dir1"
 
// recursive_directory_iterator:
// "sandbox/file2.txt"
// "sandbox/file1.txt"
// "sandbox/dir1"
// "sandbox/dir1/dir2"
```
### 4.3 space_info
- 确定路径名所在的文件系统的信息页位于，如同通过 POSIX 的 statvfs 操作一样。

- 该对象由 POSIX struct statvfs 内容进行填充如下所示：
    - space_info.capacity设置为f_blocks * f_frsize。
    - space_info.free设置为f_bfree * f_frsize。
    - space_info.available设置为f_bavail * f_frsize。
    - 任何无法确定的成员都设置为static_cast<std::uintmax_t>(-1)

```c++
#include <cstdint>
#include <filesystem>
#include <iostream>
#include <locale>
 
std::uintmax_t disk_usage_percent(const std::filesystem::space_info& si,
                                  bool is_privileged = false) noexcept
{
    if (constexpr std::uintmax_t X(-1);
        si.capacity == 0 || si.free == 0 || si.available == 0 ||
        si.capacity == X || si.free == X || si.available == X
    )
        return 100;
 
    std::uintmax_t unused_space = si.free, capacity = si.capacity;
    if (!is_privileged)
    {
        const std::uintmax_t privileged_only_space = si.free - si.available;
        unused_space -= privileged_only_space;
        capacity -= privileged_only_space;
    }
    const std::uintmax_t used_space{capacity - unused_space};
    return 100 * used_space / capacity;
}
 
void print_disk_space_info(auto const& dirs, int width = 14)
{
    (std::cout << std::left).imbue(std::locale("en_US.UTF-8"));
 
    for (const auto s : {"Capacity", "Free", "Available", "Use%", "Dir"})
        std::cout << "│ " << std::setw(width) << s << ' ';
 
    for (std::cout << '\n'; auto const& dir : dirs)
    {
        std::error_code ec;
        const std::filesystem::space_info si = std::filesystem::space(dir, ec);
        for (auto x : {si.capacity, si.free, si.available, disk_usage_percent(si)})
            std::cout << "│ " << std::setw(width) << static_cast<std::intmax_t>(x) << ' ';
        std::cout << "│ " << dir << '\n';
    }
}
 
int main()
{
    const auto dirs = {"/dev/null", "/tmp", "/home", "/proc", "/null"};
    print_disk_space_info(dirs);
}
// Possible output:

// │ Capacity       │ Free           │ Available      │ Use%           │ Dir            
// │ 84,417,331,200 │ 42,732,986,368 │ 40,156,028,928 │ 50             │ /dev/null
// │ 84,417,331,200 │ 42,732,986,368 │ 40,156,028,928 │ 50             │ /tmp
// │ -1             │ -1             │ -1             │ 100            │ /home
// │ 0              │ 0              │ 0              │ 100            │ /proc
// │ -1             │ -1             │ -1             │ 100            │ /null
```

### 4.4 symlink
- 软链接也叫符号链接，会创建一个新的inode块，里面的数据内容是链接的文件名称
- 创建一个软链接
```c++
#include <cassert>
#include <filesystem>
#include <iostream>
namespace fs = std::filesystem;
 
int main()
{
    fs::create_directories("sandbox/subdir");
    fs::create_symlink("target", "sandbox/sym1");
    fs::create_directory_symlink("subdir", "sandbox/sym2");
 
    for (auto it = fs::directory_iterator("sandbox"); it != fs::directory_iterator(); ++it)
        if (is_symlink(it->symlink_status()))
            std::cout << *it << "->" << read_symlink(*it) << '\n';
 
    assert(std::filesystem::equivalent("sandbox/sym2", "sandbox/subdir"));
    fs::remove_all("sandbox");
}

// Possible output:

// "sandbox/sym1"->"target"
// "sandbox/sym2"->"subdir"
```
- 读取软链接文件，会获取到被链接的文件本身
```c++
#include <filesystem>
#include <iostream>
 
namespace fs = std::filesystem;
 
int main()
{
    for (fs::path p : {"/usr/bin/gcc", "/bin/cat", "/bin/mouse"})
    {
        std::cout << p;
        fs::exists(p) ?
            fs::is_symlink(p) ?
                std::cout << " -> " << fs::read_symlink(p) << '\n' :
                std::cout << " exists but it is not a symlink\n" :
            std::cout << " does not exist\n";
    }
}

// Possible output:

// "/usr/bin/gcc" -> "gcc-5"
// "/bin/cat" exists but it is not a symlink
// "/bin/mouse" does not exist
```

### 4.5 status
- 就像POSIX中 stat 获取文件方式类似

```c++
#include <cstdio>
#include <cstring>
#include <filesystem>
#include <fstream>
#include <iostream>
#include <sys/socket.h>
#include <sys/stat.h>
#include <sys/un.h>
#include <unistd.h>
 
namespace fs = std::filesystem;
 
void demo_status(const fs::path& p, fs::file_status s)
{
    std::cout << p;
    // alternative: switch(s.type()) { case fs::file_type::regular: ...}
    if (fs::is_regular_file(s))
        std::cout << " is a regular file\n";
    if (fs::is_directory(s))
        std::cout << " is a directory\n";
    if (fs::is_block_file(s))
        std::cout << " is a block device\n";
    if (fs::is_character_file(s))
        std::cout << " is a character device\n";
    if (fs::is_fifo(s))
        std::cout << " is a named IPC pipe\n";
    if (fs::is_socket(s))
        std::cout << " is a named IPC socket\n";
    if (fs::is_symlink(s))
        std::cout << " is a symlink\n";
    if (!fs::exists(s))
        std::cout << " does not exist\n";
}
 
int main()
{
    // create files of different kinds
    fs::create_directory("sandbox");
    fs::create_directory("sandbox/dir");
    std::ofstream{"sandbox/file"}; // create regular file
    fs::create_symlink("file", "sandbox/symlink");
 
    mkfifo("sandbox/pipe", 0644);
    sockaddr_un addr;
    addr.sun_family = AF_UNIX;
    std::strcpy(addr.sun_path, "sandbox/sock");
    int fd = socket(PF_UNIX, SOCK_STREAM, 0);
    bind(fd, reinterpret_cast<sockaddr*>(&addr), sizeof addr);
 
    // demo different status accessors
    for (auto it{fs::directory_iterator("sandbox")}; it != fs::directory_iterator(); ++it)
        demo_status(*it, it->symlink_status()); // use cached status from directory entry
    demo_status("/dev/null", fs::status("/dev/null")); // direct calls to status
    demo_status("/dev/sda", fs::status("/dev/sda"));
    demo_status("sandbox/no", fs::status("/sandbox/no"));
 
    // cleanup (prefer std::unique_ptr-based custom deleters)
    close(fd);
    fs::remove_all("sandbox");
}
// Possible output:

// "sandbox/file" is a regular file
// "sandbox/dir" is a directory
// "sandbox/pipe" is a named IPC pipe
// "sandbox/sock" is a named IPC socket
// "sandbox/symlink" is a symlink
// "/dev/null" is a character device
// "/dev/sda" is a block device
// "sandbox/no" does not exist
```
### 4.6 hard link
- 在POSIX系统中，每个目录至少有两个硬链接，自己以及"."
- ".."有三个硬链接，目录本身，"."以及".."
- 多个文件名同时指向同一个索引节点（Inode），只增加i_nlink硬链接计数。
  > 只要文件的索引节点还存在一个以上的链接，删除其中一个链接并不影响索引节点本身和其他的链接（也就是说该文件的实体并未删除），而只有当最后一个链接被删除后，且此时有新数据要存储到磁盘上，那么被删除的文件的数据块及目录的链接才会被释放，存储空间才会被新数据所覆盖。因此，该机制可以有效的防止误删操作。

- 硬链接只能在同一类型的文件系统中进行链接，不能跨文件系统。同时它只能对文件进行链接，不能链接目录。

```c++
#include <filesystem>
#include <iostream>
namespace fs = std::filesystem;
 
int main()
{
    // On a POSIX-style filesystem, each directory has at least 2 hard links:
    // itself and the special member pathname "."
    fs::path p = fs::current_path();
    std::cout << "Number of hard links for current path is "
              << fs::hard_link_count(p) << '\n';
 
    // Each ".." is a hard link to the parent directory, so the total number
    // of hard links for any directory is 2 plus number of direct subdirectories
    p = fs::current_path() / ".."; // Each dot-dot is a hard link to parent
    std::cout << "Number of hard links for .. is "
              << fs::hard_link_count(p) << '\n';
}
// Possible output:

// Number of hard links for current path is 2
// Number of hard links for .. is 3
```

## 5 View
通常:  • 若引用范围的元素修改,则视图的元素也会修改。  • 若视图的元素修改,则引用范围的元素也会修改。
- 视图通常用于在特定的基础上,处理基础范围的元素子集和/或经过一些可选转换后的值。例  如,可以使用一个视图来迭代一个范围的前五个元素
```c++
for (const auto& elem : std::views::take(coll, 5)) {  ...  }
```
- 管道语法中让视图对范围进行操作。通过使用操作符  |,可以创建视图的管道:
  ```c++
  auto v = coll 
        | std::views::filter([](auto elem){return elem % 3 == 0;}) 
        | std::views::transform([](auto elem){return elem * elem;}) 
        |std::views::take(3);
  ```
- 通过类模板指定范围结束的值
  ```c++
  template<auto End>
  struct EndValue {
    bool operator== (auto pos) const {
        return *pos == End;  // end is where iterator points to End
    }
  };

  int main() {
    std::vector coll = {42, 8, 0, 15, 7, -1};
    std::ranges::subrange range{coll.begin(), EndValue<7>{}};
    std::ranges::sort(range);
    std::ranges::for_each(coll.begin(), EndValue<-1>{}, 
                          [](auto value){std::cout << ' ' << value;})
  }
  ```
- 支持投影功能，避免写显示的比较器，直接通过投影的方式指定使用Person的age进行排序
  ```c++
  struct Person {
    std::string name;
    int age;
  };
  std::vector<Person> people = {{"Alice", 25}, {"Bob", 30}, {"Charlie", 20}};
  std::sort(people.begin(), people.end(), 
            [](const Person& a, const Person& b) { return a.age < b.age; });
  std::ranges::sort(people, std::less<int>{}, &Person::age);
  ```
## 6 Span
```c++
// for the last 3 returned elements:
for (auto s : std::span{arrayOfConst()}.last(3)) 
// fatal runtime ERROR  
```
- 这段代码会导致未定义的行为,因为基于范围的 for 循环中存在一个 bug,在对临时对象的引用上进行迭代时会使用已经销毁的值




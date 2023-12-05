# ファイル／ディレクトリ操作
ファイル／ディレクトリ操作には、[Boost Filesystem Library](http://www.boost.org/libs/filesystem/doc/index.htm)を使用する。Boost Filesystem Libraryは、多くのプラットフォーム、コンパイラで動作する汎用的なファイル／ディレクトリ操作のライブラリである。このライブラリはビルドを必要する。


## インデックス
- [エラーハンドリング](#error-handling)
- [ファイルをコピーする](#copy-file)
- [ファイルの削除](#remove-file)
- [ファイルを移動する／ファイル名を変更する](#rename)
- [ファイルが存在するかを調べる](#exists)
- [ファイルサイズを取得する](#file-size)
- [ファイルの最終更新日時を取得する](#last-write-time)
- [パスのファイル名を取得する](#get-filename)
- [パスの拡張子を取得する](#get-extension)
- [パスの拡張子を変更する](#change-extension)
- [ディレクトリを作成する](#create-directory)
- [ディレクトリ内のファイルを列挙する](#enumerate-file)
- [ディレクトリ内の全てのファイルを再帰的に列挙](#recursive-enumerate-file)
- [C++の国際標準規格上の類似する機能](#cpp-standard)



## <a id="error-handling" href="#error-handling">エラーハンドリング</a>

Boost Filesystem Libraryのエラーハンドリングは、例外を投げるバージョン、エラーを参照で返すバージョンの2種類が存在する。

**例外バージョン**

何も指定しなければ、Boost Filesystem Libraryの関数でエラーが出た場合には`boost::filesystem::filesystem_error`例外が投げられる。

```cpp
namespace fs = boost::filesystem;
try {
    fs::foo();
}
catch (fs::filesystem_error& ex) {
    std::cout << "エラー発生！ : " << ex.what() << std::endl;
}
```


**エラーを参照で返すバージョン**

Boost Filesystem Libraryの最後の引数として、`boost::system::error_code`の変数を渡せば、例外ではなく渡したエラー用変数にエラー情報が格納される。

```cpp
namespace fs = boost::filesystem;

boost::system::error_code error;
fs::foo(error);

if (error) {
    std::cout << "エラー発生！ : " << error.message() << std::endl;
}
```


## <a id="copy-file" href="#copy-file">ファイルをコピーする</a>

ファイルをコピーするには、`boost::filesystem::copy_file()`関数を使用する。

- 第1引数はコピー元のパス
- 第2引数はコピー先のパス

コピーに失敗した場合は例外が投げられる。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/a.txt"); // コピー元
    const fs::path dest("dir2/a.txt"); // コピー先

    try {
        fs::copy_file(path, dest);
    }
    catch (fs::filesystem_error& ex) {
        std::cout << ex.what() << std::endl;
        throw;
    }
}
```

上書きコピーをする場合は、`copy_file()`関数に、`boost::filesystem::copy_option::overwrite_if_exists`オプションを指定する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/a.txt");
    const fs::path dest("dir2/a.txt");

    try {
        fs::copy_file(path, dest, fs::copy_option::overwrite_if_exists);
    }
    catch (fs::filesystem_error& ex) {
        std::cout << ex.what() << std::endl;
        throw;
    }
}
```


## <a id="remove-file" href="#remove-file">ファイルを削除する</a>

ファイルを削除するには、`boost::filesystem::remove()`を使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/a.txt");

    try {
        fs::remove(path);
    }
    catch (fs::filesystem_error& ex) {
        std::cout << ex.what() << std::endl;
        throw;
    }
}
```


## <a id="rename" href="#rename">ファイルを移動する／ファイル名を変更する</a>

ファイルの移動、ファイル名の変更には、`boost::filesystem::rename()`を使用する。

- 第1引数は、元となるファイルのパス。
- 第2引数は、移動先のファイルパス、もしくは新たなファイル名。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/a.txt");
    const fs::path dest("dir2/b.txt");

    try {
        fs::rename(path, dest);
    }
    catch (fs::filesystem_error& ex) {
        std::cout << ex.what() << std::endl;
        throw;
    }
}
```


## <a id="exists" href="#exists">ファイルが存在するかを調べる</a>

ファイルが存在するか調べるには、`boost::filesystem::exists()`関数を使用する。

この関数は、ファイルが存在する場合は`true`を返し、存在しない場合は`false`を返す。

ファイルのステータス取得に失敗した場合はエラーを返す。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/a.txt");

    boost::system::error_code error;
    const bool result = fs::exists(path, error);
    if (!result || error) {
        std::cout << "ファイルがない" << std::endl;
    }
    else {
        std::cout << "ファイルがあった" << std::endl;
    }
}
```


## <a id="file-size" href="#file-size">ファイルサイズを取得する</a>

ファイルサイズを取得するには、`boost::filesystem::file_size()`関数を使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/a.txt");

    try {
        const boost::uintmax_t size = fs::file_size(path);

        std::cout << "ファイルサイズ: " << size << std::endl;
    }
    catch (fs::filesystem_error& ex) {
        std::cout << ex.what() << std::endl;
        throw;
    }
}
```

実行結果の例:

```
ファイルサイズ: 5107200
```


## <a id="last-write-time" href="#last-write-time">ファイルの最終更新日時を取得する</a>

ファイルの最終更新日時を取得するには、`boost::filesystem::last_write_time()`関数を使用する。

この関数は戻り値として、`std::time_t`型として日時を返す。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>
#include <boost/date_time/posix_time/posix_time.hpp>

namespace fs = boost::filesystem;

int main()
{
    try {
        const fs::path path("dir1/a.txt");
        const std::time_t last_update = fs::last_write_time(path);

        const boost::posix_time::ptime time = boost::posix_time::from_time_t(last_update);
        std::cout << time << std::endl;
    }
    catch (fs::filesystem_error& ex) {
        std::cout << "エラー発生！ : " << ex.what() << std::endl;
    }
}
```

実行結果の例：

```
2011-Mar-30 05:56:11
```


## <a id="get-filename" href="#get-filename">パスのファイル名を取得する</a>

パスのファイル名を取得するには、`boost::filesystem::path`クラスの`filename()`メンバ関数を使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    fs::path p = "/a/b.txt";
    fs::path filename = p.filename();

    std::cout << filename.generic_string() << std::endl;
}
```

出力：

```
b.txt
```

## <a id="get-extension" href="#get-extension">パスの拡張子を取得する</a>

パスの拡張子を取得するには、`boost::filesystem::path`クラスの`extension()`メンバ関数を使用する。この関数が返すパスは、ドット(.)を含む。

拡張子を持たないパスに対してこの関数を適用した場合は、空のパスが返る。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    fs::path p = "/a/b.txt";
    fs::path extension = p.extension();

    std::cout << extension.generic_string() << std::endl;
}
```

出力：

```
.txt
```


## <a id="change-extension" href="#change-extension">パスの拡張子を変更する</a>

パスの拡張子を変更するには、`boost::filesystem::path`クラスの`replace_extension()`メンバ関数を使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    // 拡張子を.pngに変更する
    // replace_extension()に指定する拡張子は、
    // ドット(.)があってもなくても、どちらでもよい。
    fs::path p = "/a.txt/b.txt";
    p.replace_extension("png");

    std::cout << p.generic_string() << std::endl;
}
```

出力：

```
/a.txt/b.png
```


## <a id="create-directory" href="#create-directory">ディレクトリを作成する</a>

ディレクトリを作成するには、`boost::filesystem::create_directory()`関数を使用する。

第1引数として、ディレクトリのパスを指定する。

ネストしたディレクトリを一度に作ろうとした場合はエラーとなる。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir");

    boost::system::error_code error;
    const bool result = fs::create_directory(path, error);
    if (!result || error) {
        std::cout << "ディレクトリの作成に失敗" << std::endl;
    }
}
```

ネストしたディレクトリを含めて一度に作成するには、`boost::filesystem::create_directories()`関数を使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1/dir2");

    boost::system::error_code error;
    const bool result = fs::create_directories(path, error);
    if (!result || error) {
        std::cout << "ディレクトリの作成に失敗" << std::endl;
    }
}
```


## <a id="enumerate-file" href="#enumerate-file">ディレクトリ内のファイルを列挙する</a>

ディレクトリ内のファイルを列挙するには、`boost::filesystem::directory_iterator`クラスを使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>
#include <boost/foreach.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("dir1");

    BOOST_FOREACH(const fs::path& p, std::make_pair(fs::directory_iterator(path),
                                                    fs::directory_iterator())) {
        if (!fs::is_directory(p))
            std::cout << p.filename() << std::endl;
    }
}
```

実行結果の例

```
"a.txt"
"b.png"
```

あるいは、上記の実装例の `BOOST_FOREACH` 部分は C++11 以降で range-based-for を使いたい場合、次のようにも書ける。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>
#include <boost/range/iterator_range.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path(".");
    
    // この例の場合、 e は fs::path 型ではなく fs::directory_entry 型となる点に注意する。
    for ( const auto& e : boost::make_iterator_range( fs::directory_iterator( path ), { } ) )
        if ( ! fs::is_directory( e ) )
            std::cout << e.path().filename() << std::endl;
}
```

実行結果の例は先の `BOOST_FOREACH` バージョンと同様となるので省略する。

## <a id="recursive-enumerate-file" href="#recursive-enumerate-file">ディレクトリ内の全てのファイルを再帰的に列挙する</a>

ディレクトリ内の全てのファイルを再帰的に列挙するには、`boost::filesystem::recursive_directory_iterator`クラスを使用する。

```cpp example
#include <iostream>
#include <boost/filesystem.hpp>
#include <boost/foreach.hpp>

namespace fs = boost::filesystem;

int main()
{
    const fs::path path("D:/boost_1_49_0/boost/filesystem");

    BOOST_FOREACH(const fs::path& p, std::make_pair(fs::recursive_directory_iterator(path),
                                                    fs::recursive_directory_iterator())) {
        if (!fs::is_directory(p))
            std::cout << p << std::endl;
    }
}
```

実行結果の例

```
"D:/boost_1_49_0/boost/filesystem\config.hpp"
"D:/boost_1_49_0/boost/filesystem\convenience.hpp"
"D:/boost_1_49_0/boost/filesystem\detail\utf8_codecvt_facet.hpp"
"D:/boost_1_49_0/boost/filesystem\exception.hpp"
"D:/boost_1_49_0/boost/filesystem\fstream.hpp"
"D:/boost_1_49_0/boost/filesystem\operations.hpp"
"D:/boost_1_49_0/boost/filesystem\path.hpp"
"D:/boost_1_49_0/boost/filesystem\v2\config.hpp"
"D:/boost_1_49_0/boost/filesystem\v2\convenience.hpp"
"D:/boost_1_49_0/boost/filesystem\v2\exception.hpp"
"D:/boost_1_49_0/boost/filesystem\v2\fstream.hpp"
"D:/boost_1_49_0/boost/filesystem\v2\operations.hpp"
"D:/boost_1_49_0/boost/filesystem\v2\path.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\config.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\convenience.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\exception.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\fstream.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\operations.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\path.hpp"
"D:/boost_1_49_0/boost/filesystem\v3\path_traits.hpp"
```



## <a id="cpp-standard" href="#cpp-standard">C++の国際標準規格上の類似する機能</a>
- [`<filesystem>`](https://cpprefjp.github.io/reference/filesystem.html)

# Boost - CPP

## C++
- Anything easy is not worth doing if there is a hard thing you can do instead?
- C is not C++ - remember this.
- Linux is written in C not C++, remember this. 
- I've seen Python code optimized to do 1 billion things in about 1 second.  (albiet with additions to the code written in C++)
- The syntax is a little bit strange, but at least its not as goofy as rust. Ok()...

## Boost
- [https://www.boost.org](Boost) are C++ libraries that are keeping C++ cross-platform and modern.
- Get started at [https://www.boost.org/doc/libs/1_85_0/more/getting_started/unix-variants.html](Boost Tutorial)


Copy and past to download and unpack
```zsh
wget https://archives.boost.io/release/1.82.0/source/boost_1_82_0.tar.bz2 && 
if [[ "$(sha256sum boost_1_82_0.tar.bz2 | awk '{print $1}')" == "a6e1ab9b0860e6a2881dd7b21fe9f737a095e5f33a3a874afc6a345228597ee6" ]]; then 
  echo "Checksum matches"; 
  tar --bzip2 -xf ./boost_1_82_0.tar.bz2
else 
  echo "Checksum does not match"; 
fi
```
- there is not nothing to build.  Most of boost is header files only and easy to include. 

```c++
#include <boost/lambda/lambda.hpp>
#include <iostream>
#include <iterator>
#include <algorithm>

int main()
{
    using namespace boost::lambda;
    typedef std::istream_iterator<int> in;

    std::for_each(
        in(std::cin), in(), std::cout << (_1 * 3) << " " );
}
```
```bash
c++ -I./boost_1_82_0 example.cpp -o example
```
```bash
echo 1 2 3 | ./example
```


## Install
```bash
cd boost_1_82_0
./boostrap.sh --help
./boostrap.sh --prefix=~/booost --show-libraries --with-libraries=library-name-list
./b2 install
```

~/.zshrc
```zsh
...

# Add the include path
export BOOST_INCLUDE_DIR=$HOME/boost_1_82_0

# Add the library path
export BOOST_LIBRARY_DIR=$HOMEboost_1_82_0/stage/lib

# add to path
export PATH=$BOOST_INCLUDE_DIR:$PATH

# Add Boost include path to CPATH
export CPATH=$BOOST_INCLUDE_DIR:$CPATH

# Add Boost library path to LIBRARY_PATH and LD_LIBRARY_PATH
export LIBRARY_PATH=$BOOST_LIBRARY_DIR:$LIBRARY_PATH
export LD_LIBRARY_PATH=$BOOST_LIBRARY_DIR:$LD_LIBRARY_PATH
```

- at this point the compiled libs and header files are installed in $BOOST_INCLUDE_DIR

Type:
```
% b2 --help
```

The docs are here:
https://www.bfgroup.xyz/b2/manual/release/index.html#bbv2.overview.configuration

If you can b2 from you home dir at this point you should be all good.  


## Hello boost with b2
Make yourself a folder somewhere.

vi ./boost_graph.cpp
```C++
#include <boost/graph/adjacency_list.hpp>
#include <boost/graph/graph_traits.hpp>
#include <boost/graph/breadth_first_search.hpp>
#include <iostream>
#include <vector>

// Define the graph type using an adjacency list
typedef boost::adjacency_list<boost::vecS,
                              boost::vecS,
                              boost::undirectedS> Graph;

// Visirot to print the vertices during BFS
class bfs_visitor : public boost::default_bfs_visitor {
public:
  template <typename Vertex, typename Graph>
  void discover_vertex(Vertex u, const Graph &g) const {
    std::cout << u << " "  ;
  }
};


int main()
{
  Graph g(5);

  // Add some edges
  boost::add_edge(0, 1, g);
  boost::add_edge(0, 2, g);
  boost::add_edge(1, 3, g);
  boost::add_edge(2, 4, g);

  // Perform BFS starting from vertex 0
  bfs_visitor vis;
  boost::breadth_first_search(g,
                              boost::vertex(0, g),
                              boost::visitor(vis));

  std::cout << std::endl;
  return 0;
}

```

vi Jamfile
```
# Use GCC with specific settings
using gcc : : /usr/bin/g++ : <cxxflags>"-std=c++20 -O2" ;

exe boost_graph : boost_graph.cpp ;
```

Now you can:

```
b2
br release
./bin/release/boost_graph
0 1 2 3 4
```

You are using the boost graph data type.
Use this to verify that you are setup correctly.


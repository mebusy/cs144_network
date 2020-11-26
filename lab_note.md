

[cpp language document](https://en.cppreference.com/)

# lab0

- complie error on Centos7
    ```
    /usr/include/linux/if.h:211:19: error: field 'ifru_addr' has incomplete type 'sockaddr'
    ```
- Solution
    - in /libsponge/util/tun.cc 
    - add `#include <sys/socket.h>`  after `include "tun.hh"`

<details>
<summary>
webget impl
</summary>

```cpp
void get_URL(const string &host, const string &path) {
    // Your code here.
    TCPSocket sock2;

    // You will need to connect to the "http" service on
    // the computer whose name is in the "host" string,
    // then request the URL path given in the "path" string.
    const Address web_host( host , "http" );
    sock2.connect( web_host );

    // note: don't add any space before "\n\r"
    std::ostringstream stringStream;
    stringStream << "GET ";
    stringStream << path;
    stringStream << " HTTP/1.1\r\n";

    stringStream << "Host: " + host + "\r\n";
    stringStream << "Connection: close\r\n";
    stringStream << "\r\n";

    // cout <<  stringStream.str();

    sock2.write( stringStream.str() );

    // Then you'll need to print out everything the server sends back,
    // (not just one call to read() -- everything) until you reach
    // the "eof" (end of file).
    while ( !sock2.eof() ) {
        auto recvd = sock2.read();
        cout << recvd;
    }

    sock2.close();

    cerr << "Function called: get_URL(" << host << ", " << path << ").\n";
    cerr << "Warning: get_URL() has not been implemented yet.\n";
}
```

</details>


# lab1

- TCP implementation managed to produce a pair of reliable in-order byte streams (one from you to the server, and one in the opposite direction), even though the underlying network only delivers “best-effort” datagrams. By this we mean: short packets of data that can be lost, reordered, altered, or duplicated.
    - **re-submitted package may merge serveral raw packages.**

















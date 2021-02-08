...menustart

- [lab0](#42192835e2a0227b76b905a8183cffdd)
- [lab1](#e274b0a65912e49a28a9ae5c1479bdce)

...menuend


[cpp language document](https://en.cppreference.com/)

<h2 id="42192835e2a0227b76b905a8183cffdd"></h2>


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


<h2 id="e274b0a65912e49a28a9ae5c1479bdce"></h2>


# lab1

- TCP implementation managed to produce a pair of reliable in-order byte streams (one from you to the server, and one in the opposite direction), even though the underlying network only delivers “best-effort” datagrams. By this we mean: short packets of data that can be lost, reordered, altered, or duplicated.
- PS
    - **re-submitted package may merge serveral raw packages.**
    - package data may have 0 length


<details>
<summary>
stream_reassembler impl
</summary>

```cpp
#include "stream_reassembler.hh"

#include <fstream>

// Dummy implementation of a stream reassembler.

// For Lab 1, please replace with a real implementation that passes the
// automated checks run by `make check_lab1`.

// You will need to add private members to the class declaration in `stream_reassembler.hh`

template <typename... Targs>
void DUMMY_CODE(Targs &&... /* unused */) {}

using namespace std;

StreamReassembler::StreamReassembler(const size_t capacity) : _output(capacity), _capacity(capacity), _unassembled(), _unassembled_bytes(0) {}

//! \details This function accepts a substring (aka a segment) of bytes,
//! possibly out-of-order, from the logical stream, and assembles any newly
//! contiguous substrings and writes them into the output stream in order.
void StreamReassembler::push_substring(const string &data, const size_t index, const bool eof) {
    std::string msg = "push_substring:" ;
    msg += data;
    msg += "," + std::to_string(index);
    msg += "," + std::to_string(eof);
    Logger( msg  );

    size_t maxEndIndex2Written = _output.bytes_written() + _output.remaining_capacity();
    if ( index > maxEndIndex2Written  ) { // length 0 data-string is allowed
        msg = " ~~~ discard";
        Logger(msg);
        return;  // discard
    }

    mergeOrAddUnassembledBytes( data, index, eof );

    // from here, do NOT use function arguments
    _unassembled_bytes = 0;
    size_t _unassembled_last_index = 0;
    for (auto iter = _unassembled.begin(); iter != _unassembled.end();) {

        Unassembled_t st_unassem = iter->second ;
        auto _index = static_cast<size_t>( iter->first );
        auto _data = st_unassem.data;
        auto _eof = st_unassem.eof;



        ssize_t nByteAlreadyReassembledThisPackage = _output.bytes_written() - _index;
            msg = "nByteAlreadyReassembledThisPackage::" ;
            msg += std::to_string( nByteAlreadyReassembledThisPackage ) ;
            msg += " data: " + _data ;
            Logger( msg  );

        if ( nByteAlreadyReassembledThisPackage >= static_cast<ssize_t>( _data.length() )  ) {
            _unassembled.erase( iter++ );
            if (_eof) {
                _output.end_input();
                return;
            } else {
                // already written,  nothing to do
                continue;
            }
        }
        // can written, may partially
        if ( nByteAlreadyReassembledThisPackage >= 0  ) {
            auto _data2written = _data.substr(  nByteAlreadyReassembledThisPackage, std::min( _output.remaining_capacity(), _data.length()-nByteAlreadyReassembledThisPackage ) );
            _output.write( _data2written ) ;

            msg = "write::" ;
            msg += _data2written ;
            msg += " raw string: " + _data ;
            Logger( msg  );

            _unassembled.erase( iter++ );

            if (_data2written.length() + nByteAlreadyReassembledThisPackage == _data.length() ) {
                // all data is written
                if (_eof) {
                    _output.end_input();
                    return;
                }
            } else {
                // partial date left
                mergeOrAddUnassembledBytes( _data.substr( _data2written.length() + nByteAlreadyReassembledThisPackage ) , _index+_data2written.length() , _eof );
            }
            continue;
        } else {
            // unassembled
            if ( _index > _unassembled_last_index ) {
                _unassembled_bytes += _data.length();
                _unassembled_last_index = _index + _data.length();
            } else {
                // contain dup data
                if ( _index + _data.length() <= _unassembled_last_index ) {
                    // entirely dup data
                    _unassembled.erase( iter++ );
                    continue;
                } else {
                    // partial dup data
                    _unassembled_bytes += _index + _data.length() - _unassembled_last_index ;
                    _unassembled_last_index = _index + _data.length();
                }
            }
        } // end
        iter++;
    } // end for
}

size_t StreamReassembler::unassembled_bytes() const { return _unassembled_bytes  ; }

bool StreamReassembler::empty() const { return unassembled_bytes() == 0; }

void StreamReassembler::mergeOrAddUnassembledBytes(const string &data, const size_t index, const bool eof) {
    // insert only not exists
    if ( _unassembled.find( index ) == _unassembled.end() ) {
        Unassembled_t st_unassem;
        st_unassem.data = data ;
        st_unassem.eof = eof ;

        _unassembled[index] =  st_unassem;
    } else {
        auto iter = _unassembled.find(index);
        Unassembled_t st_unassem = iter->second ;
        if ( data.length() > st_unassem.data.length() ) { // more data
            st_unassem.data = data ;
            st_unassem.eof = eof ;
        }
        _unassembled[index] =  st_unassem;
    }
}

void StreamReassembler::Logger( std::string &logMsg ){

    ofstream ofs( "debug_log.txt", std::ios_base::out | std::ios_base::app );
    ofs << logMsg << '\n';
    ofs.close();
}

```

</details>















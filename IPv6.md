...menustart

- [IPv6](#fdb7cb8f657426e7c409bfd6d1a36ce4)
    - [Address Structure](#47d2b1ca19f15df7102b1b7424ae08ab)
    - [How to assignment ? Example Approcach (RFC 4291)](#01b16ac39508c924e127c0de66a45bf1)

...menuend


<h2 id="fdb7cb8f657426e7c409bfd6d1a36ce4"></h2>


# IPv6

- Word started in 1994
- Basic protocol published in 1998 (RFC 2460)
- Lull, then increased interested in 2003-2006
- Hard push within the IEFT today for adoption

<h2 id="47d2b1ca19f15df7102b1b7424ae08ab"></h2>


## Address Structure

- IPv6 has 128 bits of address
- Separated into subnet and interface portions (RFC 4291)
    - `subnet prefix(n) |   interface ID (128-n)`
- Write address in hexidecimal as 8 blocks of 16 bits, separated by `:`
    - market.scs.stanford.edu: 2001:470:806d:1::9  prefixlen 64
    - Can omit a single run of zeros with `::`
        - 2001:470:806d:1:0:0:0:9
    - Use brackets in URLs : http://[2001:470:806d:1::9]:80
    - Can write low 32 bits like IPv4
        - 64:ff9b::171.66.3.9


<h2 id="01b16ac39508c924e127c0de66a45bf1"></h2>


## How to assignment ? Example Approcach (RFC 4291)

- Can auto-generate IPv6 address from subnet /64 and Ethernet address
- Ethernet devices have a 48-bit unique identifier (layer 2 address)
    - Specified at manufacturing, today you can typically change it if you want.
    - Manufacturer code(c) and assigned value(m), g is 0 for unicast MAC address
        - `|cccccc0g|cccccccc|cccccccc|mmmmmmmm|mmmmmmmm|mmmmmmmm|`
- Convert 48-bit Ethernet address to 64-bit interface ID by flipping 0, sticking 0xfffe in middle
    - `|cccccc1g|cccccccc|cccccccc|11111111|11111110|mmmmmmmm|mmmmmmmm|mmmmmmmm|`


on server side:

- declare and initialize bind_addr
- declare and initialize bind_port
<details>
  <summary> func main: </summary>

  - declare server tcp file descriptor
  - call create socket and initialize tcp fd with return value from this func
  - checking error after calling create socket
  - start event loop and pass the tcp fd as param
  - close tcp fd and exit program
</details>

<details>
  <summary> func create socket that return tcp fd: </summary>

  - declare the server socket address with struct sockaddr_in
  - declare the ret variable for error handling
  - declare tcp file descriptor to handle socket
  - intialize the tcp fd with return value from calling socket syscall
    - passing address family IPv4 (AF_INET) constant as first param
    - passing socket type tcp (SOCK_STREAM) constant as second param
    - passing internet protocol type (IPPROTO_TCP) constant as third param
  - checking error after create socket and return -1 if error
  - initialize `socketaddr_in` with memset
  - assign AF_INET (address family for IPv4) constant to sin_family property from `socketaddr_in`
  - convert bind address string to binary - with help of func inet_pton from library
    - assign `socketaddr_in.sin_addr` by passing the `socketaddr_in.sin_addr` as third parameter to inet_pton
  - check for error from return value of inet_pton
  - if error, close the tcp fd and return -1
  - convert bind port's byte order to big endian with func htons and assign `socketaddr_in.sin_port` from return value of htons
  - binding with bind syscall
    - assign ret var from return value of bind
    - casting the struct `socketaddr_in` to generic struct `socketaddr`
  - check for error, if error close the tcp fd and return -1
  - listen to incoming request by calling listen syscall and assign ret var from return value of listen
  - check for error, if error close the tcp fd and return -1
  - return tcp fd
</details>

<details>
  <summary> func start event loop </summary>

  - declare ret var
  - loop with while
    - initialize ret var with return value from run_event_loop func
      - pass tcp fd as parameter of run_event_loop
    - check for an error or exit signal, and break if the conditions are met
</details>

<details>
  <summary> func run event loop </summary>

  - declare the client socket address with struct sockaddr_in
  - declare the address length with socklen_t
  - declare the client tcp fd
  - optional section
    - declare the client address in the form of string
    - declare client port
  - initialize address length with `sizeof(socklen_t)`
  - waiting for connection from client by accept func from syscall

    - casting the struct `socketaddr_in` to generic struct `socketaddr`
    - passing tcp fd as first param
    - passing generic sockaddr as second param
    - passing address length as third param

  - initialize client fd by return value from accept
  - check error by client fd and return -1 if error
  - optional section

    - use inet_ntop and ntohs to make the data readable on your machine/host
    - and printf the result of inet_ntop and ntohs

  - call receive data func
    - pass the client fd as param
  - close the client fd
  - return 0 if there's no error
</details>

<details>
  <summary> func receive data </summary>

  - declare pointer data with simple custom protocol from struct data
  - declare ret var for checking purposes
  - allocate pointer data with malloc
  - checking malloc error
  - do a looping with while
    - waiting message from client with recv syscall
    - initialize ret var with return value from recv
    - check error, break if error
    - check if client disconnected, break if disconnected
    - call interpret message (to interpret client message)
      - pass pointer data
    - call get input and send
      - pass client fd and pointer data
      - check for return value
        - if true then break the loop
  - free the data memory
</details>

on both-side:

- declare and initialize struct `data` as super simple protocol
<details>
  <summary> func interpret message </summary>
  
  - assign len with ordered byte from htons
  - add null char from msg to terminate string
  - then you be able to print the client msg
</details>

<details>
  <summary> func get input and send </summary>
  
  - declare len var to store msg length
  - call fgets to enter the message from stdin
    - pass msg as first param
    - and the size of msg as second param
    - and the source (stdin)
    - check the return value
      - if false (indicate EOF) return -1
  - count length msg with strlen and assign it to len var
  - check the last index of msg with \n char
    - assign last index of msg with null to terminate string
    - decrement len var
  - check if the msg is empty (len var eq 0)
    - return 0 (don't send data to dest)
  - if msg exit return -1
  - reorder byte of len with htons
  - call send data to dest
    - passing dest fd
    - passing pointer data
  - return 0 to continue iterate the loop
</details>

<details>
  <summary> func send data to dest </summary>
  
  - declare ret for checking
  - call send syscall
    - initialize ret with return value
    - pass the proper param
  - check if ret var indicate error
    - return -1 if error
  - return 0 if not
</details>

on client:

- declare and initialize server_addr
- declare and initialize server_port
<details>
  <summary> func main </summary>
  
  - kurang lebih sama kek server
</details>

<details>
  <summary> func create_socket_and_connect </summary>
  
  - kurang lebih sama kek server
  - but instead of create, bind, and listen. just create and connect
</details>

<details>
  <summary> func start_chat </summary>
  
  - event loop happens here
</details>

additional function from lib:

- inet_pton presentation to network | string to binary
- inet_ntop binary to string
- htons manipulate endianness
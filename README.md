- Name: Janssen Benedict
- Class: Pemrograman Lanjut A
- NPM: 2306152102

# Commit 1 Reflection notes

- This is a simple web-server program utilizing Rust for the implementation, it receives HTTP requests and prints out the details of said request.
- On the main function, a TCP listener is made and bound to the localhost on port 7878, so it will 'listen' for incoming connections made from there. Then, each incoming TcpStream connection will be passed down to the handle_connection method.
- The handle_connection method receives the TcpStream variable (named 'stream') through its parameters as a mutable variable. The mutability of the variable is necessary so that it could be modified when needed.
- The buf_reader variable utilizes BufReader::new(&mut stream) in order to read from the stream, which modifies its internal state, thus requiring the variable to be the mutable.
- The HTTP request from the reader is then stored into a vector, represented by the http_request variable.
- The lines() method allows the program to read the stream line-by-line. It will return an iterator over the lines of the reader (Result<String, Error>).
- The reading results are then converted into strings through the unwrap() method within the map() method.
- Each line from the iterator will be taken until an empty line is found. This is because an empty line signifies the end of HTTP request headers ('.take_while(|line| !line.is_empty())').
- Finally, the collect() method stores all the lines that were read into a vector of strings (Vec<String>), which is then printed out by the function.
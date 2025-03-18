- Name: Janssen Benedict
- Class: Pemrograman Lanjut A
- NPM: 2306152102

# Module 6 - Concurrency

### Commit 1 Reflection notes

- This is a simple web-server program utilizing Rust for the implementation, it receives HTTP requests and prints out the details of said request.
- On the main function, a TCP listener is made and bound to the localhost on port 7878, so it will 'listen' for incoming connections made from there. Then, each incoming TcpStream connection will be passed down to the handle_connection() method.
- The handle_connection method receives the TcpStream variable (named 'stream') through its parameters as a mutable variable. The mutability of the variable is necessary so that it could be modified when needed.
- The buf_reader variable utilizes BufReader::new(&mut stream) in order to read from the stream, which modifies its internal state, thus requiring the variable to be the mutable.
- The HTTP request from the reader is then stored into a vector, represented by the http_request variable.
- The lines() method allows the program to read the stream line-by-line. It will return an iterator over the lines of the reader (Result<String, Error>).
- The reading results are then converted into strings through the unwrap() method within the map() method.
- Each line from the iterator will be taken until an empty line is found. This is because an empty line signifies the end of HTTP request headers ('.take_while(|line| !line.is_empty())').
- Finally, the collect() method stores all the lines that were read into a vector of strings (Vec<String>), which is then printed out by the function.
- The use of unwrap() in this code is done to ensure that a proper result value is returned. If not, it would make the program panic with an error.

### Commit 2 Reflection notes

![](/images/Module 6/milestone_2.png)

- The modifications made to the handle_connection() method, as well as the creation of a HTML file (hello.html), allow for a HTML response to be returned and the HTML page to be viewed on the browser when accessing the localhost on port 7878.
- Just like the previous commit, the TcpStream variable (named 'stream') is received through the parameters as a mutable variable, then the stream is read line-by-line until an empty line is found, signifying the end of the HTTP request header.
- The status_line variable contains the HTTP 200 status code that indicates the request made was successful.
- The contents variable utilizes fs::read_to_string("hello.html").unwrap(); in order to read the hello.html file's contents and save them in the form of a string.
- The length variable gets the length of the file's contents by utilizing the len() method.
- The response variable formats the HTTP response by utilizing the contents of the variables made beforehand and formatting them with format!(). The contents of 'status_line' becomes the status line, the contents of 'length' becomes the Content-length information for the headers, and the contents of 'contents' becomes the actual HTML file contents in the response body. Then, stream.write_all(response.as_bytes()).unwrap() returns the entire response to the browser. It converts the response (which is in the form of a string) into bytes and writes those bytes to the stream.
- The use of unwrap() in this code is done to ensure that a proper result value is returned. If not, it would make the program panic with an error.
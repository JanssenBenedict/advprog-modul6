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

![](/images/module_6/milestone_2.png)

- The modifications made to the handle_connection() method, as well as the creation of a HTML file (hello.html), allow for a HTML response to be returned and the HTML page to be viewed on the browser when accessing the localhost on port 7878.
- Just like the implementation on the previous commit, the TcpStream variable (named 'stream') is received through the parameters as a mutable variable, then the stream is read line-by-line until an empty line is found, signifying the end of the HTTP request header.
- The status_line variable contains the HTTP 200 status code that indicates the request made was successful.
- The contents variable utilizes fs::read_to_string("hello.html").unwrap(); in order to read the hello.html file's contents and save them in the form of a string.
- The length variable gets the length of the file's contents by utilizing the len() method.
- The response variable formats the HTTP response by utilizing the contents of the variables made beforehand and formatting them with format!(). The contents of 'status_line' becomes the status line, the contents of 'length' becomes the Content-length information for the headers, and the contents of 'contents' becomes the actual HTML file contents in the response body. Then, stream.write_all(response.as_bytes()).unwrap() returns the entire response to the browser. It converts the response (which is in the form of a string) into bytes and writes those bytes to the stream.
- The use of unwrap() in this code is done to ensure that a proper result value is returned. If not, it would make the program panic with an error.

### Commit 3 Reflection notes

![](/images/module_6/milestone_3.png)

- The modifications made to the handle_connections() method, as well as the creation of the 404.html file, allow for proper HTTP request URL validation and returning the appropriate response.
- Instead of using the http_request variable to store the request in a vector and read it line-by-line, the request_line variable reads the first line of the request, which is the status line, utilizing .lines().next(). To make sure that the status line is returned properly, .unwrap().unwrap() is utilized so that the program panics with an error if it doesn't.
- The (status_line, file_name) tuple performs a conditional statement that is meant to split the resulting HTTP response into two possibilities. If the request's status line stored in request_line is 'GET / HTTP/1.1', it will properly set the response status_line to the 200 OK status code and the file_name to the proper HTML file that should display on the webpage, which is hello.html. If the request's status line consists of anything other than that, then the status_line will be set to the 404 NOT FOUND status code and the file_name will be set to the 404.html file, the error page that will be displayed on the webpage.
- Just like the implementation on the previous commit, the contents, length, and response variables are utilized for the same purposes. stream.write_all(response.as_bytes()).unwrap() will still convert the formed response into bytes and write it to the stream, thus returning the entire response to the browser (and it makes sure that the proper results are returned, otherwise the program would panic with an error).
- Before the refactoring was done, the two cases on the if and else blocks were quite repetitive and unnecessarily large.
```bash
    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();
        let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();
        let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
        stream.write_all(response.as_bytes()).unwrap();
    }
```
After receiving the correct status lines, both cases receive the contents of the appropriate HTML file as a string, receive the length of the contents, format the HTTP response, and write said response to the stream. In order to get rid of the unnecessary repetition of the code, refactoring needs to be done.
```bash
    let (status_line, file_name) =
        if request_line == "GET / HTTP/1.1" {
            ("HTTP/1.1 200 OK", "hello.html")
        } else {
            ("HTTP/1.1 404 NOT FOUND", "404.html")
        };
    let contents = fs::read_to_string(file_name).unwrap();
    let length = contents.len();
    let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
    stream.write_all(response.as_bytes()).unwrap();
```
I refactored the code by only inserting the values that differ between both cases into the conditional statement, which are the values for the response status line and the HTML file name. Afterward, the method execution continues by receiving the contents of the appropriate HTML file as a string, receiving the length of the contents, formatting the HTTP response, and writing said response to the stream. With those operations now taking place outside the if and else block, there is no unneeded duplication of the code.

### Commit 4 Reflection notes

- The modifications made to the handle_connections() method allow for a delayed response to a HTTP request that was made.
- After receiving the request's status line, the handle_connections() method checks to see what the request's status line actually contains. If a request is sent to the /sleep endpoint (http://127.0.0.1:7878/sleep), then the request would contain the status line 'GET /sleep HTTP/1.1'. On this endpoint, a simulation of a delayed or slow response would be executed. This is meant to simulate certain cases in real life where the server might take significantly more time than usual to complete executing a process.
- In order to simulate slow responses when the /sleep endpoint is accessed, I utilized thread::sleep(Duration::from_secs(10)) to delay or pause the current thread for 10 seconds. Because the web server is single-threaded, any further requests made would not be processed until the request (with the slow response) from the /sleep endpoint is handled.

### Commit 5 Reflection notes

- The modifications made to the main.rs file and the creation of the lib.rs file are made to convert the single-threaded web server into a multithreaded web server.
- ThreadPool is the data structure used in this project in order to achieve the implementation of a multithreaded web server. A ThreadPool can help manage the existence of multiple threads running parallel to one another, allowing for multiple tasks or jobs to be executed much more effectively than a standard single threaded implementation.
- In the lib.rs file, we declare a few structures necessary for this implementation, such as Job and Worker. The Job type alias is a boxed closure containing the 'job' or function that will be executed that can also be sent across threads (+ Send). The Worker structure represents a worker thread that is meant to execute an incoming task or job (the system thread executing the job is thread::JoinHandle<()>).
- The ThreadPool stores a vector of worker threads and sends over jobs to be executed by the worker threads (through mpsc channel). So, when a request is made, the ThreadPool's execute() method will box it into a Job closure and send it over to the job queue, which is Arc<Mutex<mpsc::Receiver<Job>>>. A worker thread from the pool locks the queue, accept the job, and execute it. The job is processed and the proper response gets returned. If another request is made while the previous job is still in the middle of executing, then another worker will take that job and execute it.

### Commit Bonus Reflection notes

- The new() function has been replaced with a build() function. In this build() function, I have added a conditional statement which states that if the size of the ThreadPool to be made is zero or negative, then it is not a valid ThreadPool size. The building process fails and an error message stating that fact is returned.
- On the main.rs file, when building the ThreadPool needs to be done, if the given argument for the size is zero or lesser than that, an accurate error message would be returned so that the program doesn't end up building a ThreadPool with no threads or a negative amount of threads to be executed.
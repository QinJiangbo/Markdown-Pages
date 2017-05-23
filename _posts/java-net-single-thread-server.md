---
title: Java网络编程之单线程Client-Server
date: 2016-05-12 22:54:05
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java-net-thumbnail.png
tags:
	- 网络编程
	- Java
categories:
	- 开发技术
	- Java
keywords:
	- 网络编程
	- Java
---
### Java网络编程知识整理如下思维导图:
![Java网络编程](https://obrxbqjbi.qnssl.com/blog/image/java-net-thumbnail.png)

单线程的C/S架构实现如下：

+ Client:

``` java
package exercise01;  
import java.io.*;  
import java.net.*;  
  
public class Client {  
    private String hostname;  
    private int port;  
    Socket socket = null;  
      
    public Client(String hostname, int port){  
        //constructor of the Client class  
        this.hostname = hostname;  
        this.port = port;  
    }  
      
    public void connect() throws UnknownHostException, IOException{  
        System.out.println("Attempting connect to "+ hostname +":"+port);  
        socket = new Socket(hostname,port);  
        System.out.println("Connection established!");  
    }  
      
    public void readResponse() throws IOException{  
        String userInput;  
        BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));  
        System.out.println("Response from the server:");  
        while((userInput = reader.readLine() )!= null ){  
            System.out.println(userInput);  
        }  
    }  
      
    public static void main(String[] argv){  
        //create an object for the current class Client  
        Client client = new Client("localhost",8181);  
        try{  
            //trying to establish a connection to the server  
            client.connect();  
              
            //if connection succeed, return the input contents  
            client.readResponse();  
              
        }catch(UnknownHostException ukhe){  
            //if the host not found  
            System.err.println("Host unknown! Connection can not be established!");  
        }catch(IOException ioe){  
            //if the server doesn't work  
            System.err.println("Connection can not be established! The server may not be on! Check the error message! "+ioe.getMessage());  
        }  
    }  
}  
```

+ Server

``` java
package exercise01;  
  
import java.net.*;  
import java.io.*;  
  
public class Server {  
    private ServerSocket serverSocket;  
    private int port;  
      
    public Server(int port){  
        this.port = port;  
    }  
      
    public void startServer() throws IOException{  
        System.out.println("Starting the socket server at the port: " + port);  
        serverSocket = new ServerSocket(port);  
          
        //Listen the clients. Block until one connects  
        System.out.println("Waiting for clients...");  
        Socket client = serverSocket.accept();  
          
        //A client has connected and send the welcome message  
        sendMessage(client);  
    }  
  
    //send the display message  
    private void sendMessage(Socket client) throws IOException {  
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(client.getOutputStream()));  
        writer.write("Hello. You are connected to a Simple Socket Server.");  
        writer.flush();  
        writer.close();  
    }  
      
    /** 
     * Create a server object and start the server 
     *  
     * @param argv 
     */  
    public static void main(String[] argv){  
        int port = 8181;  
          
        //start the server  
        try{  
            Server server = new Server(port);  
            server.startServer();  
        }catch(IOException ioe){  
            ioe.printStackTrace();  
        }  
    }  
}  
```
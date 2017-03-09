---
title: Java网络编程之多线程Client-Server
date: 2016-05-13 23:01:00
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

Java C/S架构网络编程的多线程版相比单线程版更复杂，同时效率也更高。

+ Client

``` java
package exercise01;  
import java.io.*;  
import java.net.*;  
  
public class ThreadedClient {  
    private String hostname;  
    private int port;  
    Socket socket = null;  
      
    public ThreadedClient(String hostname, int port){  
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
      
    public void askForTime() throws IOException{  
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));  
        writer.write("Time?");  
        writer.newLine();  
        writer.flush();  
    }  
      
    public static void main(String[] argv){  
        //create an object for the current class Client  
        ThreadedClient client = new ThreadedClient("localhost",8181);  
        try{  
            //trying to establish a connection to the server  
            client.connect();  
              
            //ask the server for time  
            client.askForTime();  
              
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
import java.io.*;  
import java.net.*;  
import java.util.Date;  
  
public class ThreadedServer {  
    private ServerSocket serverSocket;  
    private int port;  
      
    public ThreadedServer(int port){  
        this.port = port;  
    }  
      
    //get the thread started  
    public void start() throws IOException{  
        System.out.println("Starting the server at the port: "+ port);  
        serverSocket = new ServerSocket(port);  
          
        //instantial an object client  
        Socket client = null;  
          
        //set the condition to be true for endless loops  
        while(true){  
            System.out.println("Waiting for clients...");  
            client = serverSocket.accept();  
            System.out.println("The following client has connected: " + client.getInetAddress().getCanonicalHostName());  
            //A client has connected to the server  
            Thread thread = new Thread(new SocketClientHandler(client));  
            thread.start();  
        }  
    }  
      
    /** 
     * instantial an object of class ThreadedServer and start the server 
     *  
     * @param argv 
     */  
    public static void main(String[] argv){  
        //set the default port number  
        int port = 8181;  
          
        try{  
            //initializing the socket server  
            ThreadedServer threadedServer = new ThreadedServer(port);  
            threadedServer.start();  
        }catch(IOException ioe){  
            ioe.printStackTrace();  
        }  
    }  
}  
  
class SocketClientHandler implements Runnable{  
  
    private Socket client;  
      
    public SocketClientHandler(Socket client){  
        this.client = client;  
    }  
      
    /** 
     * get the thread started by the run method 
     */  
    @Override  
    public void run() {  
        try{  
            System.out.println("Thread started with name: " + Thread.currentThread().getName());  
            readResponse();  
        }catch(IOException ioe){  
            ioe.printStackTrace();  
        }catch(InterruptedException ie){  
            ie.printStackTrace();  
        }  
    }  
  
    /** 
     * read the response from the server 
     * @throws IOException 
     * @throws InterruptedException 
     */  
    private void readResponse() throws IOException, InterruptedException {  
        String userInput;  
        BufferedReader reader = new BufferedReader(new InputStreamReader(client.getInputStream()));  
        while(( userInput = reader.readLine())!= null){  
            if(userInput.equals("Time?")){  
                System.out.println("Request to send time! Sending time current.");  
                sendTime();  
                break;  
            }  
        }  
    }  
  
    /** 
     * send the current time to the server 
     * @throws IOException 
     * @throws InterruptedException 
     */  
    private void sendTime() throws IOException, InterruptedException {  
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(client.getOutputStream()));  
        writer.write(new Date().toString());  
        writer.flush();  
        writer.close();  
    }  
}  
```
# Secure_chatApplication
Midterm Project: Secure Messaging Application 

Submitted by: Altamash Arif Karlekar (A00281563) 

 

 

1. Introduction 

The Secure Messaging System is a text-based application designed to facilitate user registration, authentication, and direct messaging between registered users. This report provides an overview of the application's code structure, dependencies, and its intended workflow. 

 

2. Dependencies 

The application relies on several Rust libraries to implement its functionality: 

rusqlite: This library provides a way to interact with SQLite databases, allowing for the creation, querying, and updating of data. 

argon2rs: Used for password hashing. Argon2 is a memory-hard hashing function that is resistant to GPU-based attacks and is considered one of the most secure password hashing methods available. 

rand: Enables the generation of random values, which is crucial for creating salts for password hashing. 

hex: Provides functionality to encode and decode hexadecimal values, particularly useful when dealing with password hashes. 

rpassword: Allows for the password to be read from the terminal without displaying it, adding a layer of security during user input. 

 

3. Application-level Encryption: 

TDE(Transparent Data Encryption): 

is a type of encryption where the database itself manages the encryption and decryption transparently. This means you, as the user or application developer, don't have to make any changes to your application for data to be encrypted in storage. 

What is it? 

 

    Encryption is done at the application layer, not by the database. 

How does it work? 

 

    Before data is sent to the database, it's encrypted by the application. 

    After data is retrieved from the database, it's decrypted by the application. 

 

3. Code Structure & Overview 

The application follows a structured flow with distinct functions catering to various functionalities: 

main(): Serves as the application's entry point. It initializes the database and presents the main menu to the user. 

init_db(): Sets up the necessary database tables if they don't already exist. 

register(): Facilitates user registration, ensuring password security by hashing it alongside a generated salt. 

login(): Authenticates users by matching the hashed input password with the stored hash. 

generate_salt(): Generates a random salt for password hashing. 

get_user_input(): A utility function to fetch user input from the console. 

4. Interaction Map & Wireframe 

Interaction Map 

The user is presented with a series of choices throughout the application: 

Startup: The program is opened and initializes the database. 

Main Menu: 

Register: User can create a new account. 

Login: Existing users can authenticate themselves. 

Quit: Close the application. 

Chat Interface: 

 

After logging in, the user can: 

View list of registered users. 

Start a new chat or select an existing chat thread. 

Send and receive messages. 

Logout to return to the main menu. 

 

Wireframe Diagram 

The application's interface is text-based, suitable for a terminal or command-line environment: 

 

------------------------------------------ 

  SECURE MESSAGING SYSTEM 

------------------------------------------ 

1. Register 

2. Login 

3. Quit 

Choice: _ 

... 

[If Register/Login is selected] 
------------------------------------------ 
Enter Username: ________ 
Enter Password: ________ [Hidden Input] 
------------------------------------------ 
 
[After successful login] 
------------------------------------------ 
Logged in as: USERNAME 
------------------------------------------ 
1. View Users 
2. Start Chat with [User] 
3. View Chat with [User] 
4. Send Message to [User] 
5. Logout 
------------------------------------------ 
Choice: _ 
 
[If Start Chat or View Chat is selected] 
------------------------------------------ 
Chat with [User] 
------------------------------------------ 
[User]: Previous message content... 
You: Your last sent message... 
------------------------------------------ 
Type your message: ________ 

------------------------------------------ 

  SECURE MESSAGING SYSTEM 

------------------------------------------ 

1. Register 

2. Login 

3. Quit 

------------------------------------------ 

Choice: _ 

  

[If Register/Login is selected] 

------------------------------------------ 

Enter Username: ________ 

Enter Password: ________ [Hidden Input] 

------------------------------------------ 

  

[After successful login] 

------------------------------------------ 

Logged in as: USERNAME 

------------------------------------------ 

1. View Users 

2. Start Chat with [User] 

3. View Chat with [User] 

4. Send Message to [User] 

5. Logout 

------------------------------------------ 

Choice: _ 

  

[If Start Chat or View Chat is selected] 

------------------------------------------ 

Chat with [User] 

------------------------------------------ 

[User]: Previous message content... 

You: Your last sent message... 

------------------------------------------ 

Type your message: ________ 

 

 

5. Implementation 

The application is built on the Rust programming language, ensuring memory safety and efficient performance. Key highlights of the implementation include: 

    User Registration: Passwords are not stored in plain text. Instead, they're hashed using the Argon2 hashing algorithm alongside a unique salt, ensuring security even if the database is compromised. 

    User Authentication: During login, the entered password is hashed using the stored salt for that user, and the resultant hash is matched against the stored hash to authenticate. 

    Database Integration: SQLite, a serverless SQL database engine, is utilized for data storage. This provides the benefits of relational databases without the need for a dedicated database server. 

    Chat Functionality: Users can send and receive messages in real-time, with the data securely stored in the database. 

 

 

The biggest problem I faced:  

Database connection issue while implementing the hash and retrieval of password form hex. 

 

    I want to build a more user friendly interface for the chat system/Application. 

 

 

 

 

Screenshots of the code: 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

Screenshots of Code Working:  

 

    Chat system opening: with registration, login and exit tab 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

    User1 “Alta11” sends a message to User2 “Alta12” 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

    User2 “Alta12” Receives the message and sends back a reply to user1 “Alta11” 

 

 

 

 

 

 

This chat application is currently a console/cli based but I'm working on to include this with js (java script) to make it a full fledged web based chat application with complete secure chat monitoring  

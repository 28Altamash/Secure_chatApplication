use rusqlite::{Connection, Result, OptionalExtension};
use argon2rs::{Argon2, Variant};
use rand::Rng;
use hex;
use rpassword;

const DB_PATH: &str = "./secure_messaging.db";

fn main() -> Result<()> {
    let conn = Connection::open(DB_PATH)?;
    init_db(&conn)?;

    loop {
        println!("1. Register");
        println!("2. Login");
        println!("3. Quit");

        let choice: u8 = get_user_input().trim().parse().unwrap_or(0);

        match choice {
            1 => register(&conn)?,
            2 => login(&conn)?,
            3 => break,
            _ => println!("Invalid choice!"),
        }
    }

    Ok(())
}

fn generate_salt() -> [u8; 16] {
    let mut salt = [0u8; 16];
    rand::thread_rng().fill(&mut salt);
    salt
}

fn init_db(conn: &Connection) -> Result<()> {
    conn.execute(
        "CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, username TEXT UNIQUE, password_hash TEXT, salt BLOB)",
        [],
    )?;

    conn.execute(
        "CREATE TABLE IF NOT EXISTS messages (
            id INTEGER PRIMARY KEY,
            sender TEXT NOT NULL,
            receiver TEXT NOT NULL,
            message TEXT NOT NULL,
            timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
        )",
	[],
    )?;

    Ok(())
}

fn get_user_input() -> String {
    let mut input = String::new();
    std::io::stdin().read_line(&mut input).unwrap();
    input
}

fn register(conn: &Connection) -> Result<()> {
    println!("Enter username:");
    let username = get_user_input().trim().to_string();

    println!("Enter password:");
    let password = rpassword::read_password_from_tty(Some("Password: ")).unwrap();

    let salt = generate_salt();
    let mut password_hash = [0u8; 32];
    let argon2 = Argon2::new(2, 8, 1 << 16, Variant::Argon2i).unwrap();
    argon2.hash(&mut password_hash, password.as_bytes(), &salt, &[], &[]);

    conn.execute(
        "INSERT INTO users (username, password_hash, salt) VALUES (?1, ?2, ?3)",
        &[&username, &hex::encode(&password_hash), &hex::encode(&salt)],
    )?;

    println!("Registration successful");

    Ok(())
}

fn login(conn: &Connection) -> Result<()> {
    println!("Enter username:");
    let username = get_user_input().trim().to_string();

    println!("Enter password:");
    let password = rpassword::read_password_from_tty(Some("Password: ")).unwrap();

    let mut stmt = conn.prepare("SELECT password_hash, salt FROM users WHERE username = ?1")?;
    let stored_data: Option<(String, String)> = stmt.query_row(&[&username], |row| {
        Ok((row.get(0)?, row.get(1)?))
    }).optional()?;

    if let Some((stored_hash, stored_salt)) = stored_data {
        let stored_hash_bytes = hex::decode(stored_hash).unwrap();
        let stored_salt_bytes = hex::decode(stored_salt).unwrap();

        let mut password_hash = [0u8; 32];
        let argon2 = Argon2::new(2, 8, 1 << 16, Variant::Argon2i).unwrap();
        argon2.hash(&mut password_hash, password.as_bytes(), &stored_salt_bytes, &[], &[]);

        if stored_hash_bytes == password_hash {
            println!("Login successful!");
            chat_loop(conn, &username)?;
        } else {
            println!("Invalid username or password!");
        }
    } else {
        println!("Invalid username or password!");
    }

    Ok(())
}

fn chat_loop(conn: &Connection, username: &str) -> Result<()> {
    loop {
        println!("1. Send a message");
        println!("2. Receive messages");
        println!("3. Exit chat");

        let choice: u8 = get_user_input().trim().parse().unwrap_or(0);

        match choice {
            1 => send_message(conn, &username)?,
            2 => receive_messages(conn, &username)?,
            3 => break,
            _ => println!("Invalid choice!"),
        }
    }

    Ok(())
}

fn send_message(conn: &Connection, sender: &str) -> Result<()> {
    println!("Enter receiver's username:");
    let receiver = get_user_input().trim().to_string();

    println!("Enter your message:");
    let message = get_user_input().trim().to_string();

    conn.execute(
        "INSERT INTO messages (sender, receiver, message) VALUES (?1, ?2, ?3)",
        &[sender, &receiver, &message],
    )?;

    println!("Message sent!");

    Ok(())
}

fn receive_messages(conn: &Connection, username: &str) -> Result<()> {
    let mut stmt = conn.prepare("SELECT sender, message, timestamp FROM messages WHERE receiver = ?1")?;
    let rows = stmt.query_map(&[&username], |row| {
        Ok((
            row.get(0)?,
            row.get(1)?,
            row.get(2)?
        ))
    })?;

    let mut found = false;
    for message in rows {
        let (sender, content, timestamp): (String, String, String) = message?;
        println!("[{}] {}: {}", timestamp, sender, content);
        found = true;
    }

    if !found {
        println!("No new messages.");
    }

    Ok(())
}

from flask import Flask, request
import sqlite3
import os

app = Flask(__name__)

# Ensure consistent DB name
db_path = 'database.db'

# Create the DB and table if they don't exist
if not os.path.exists(db_path):
    conn = sqlite3.connect(db_path)
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            username TEXT NOT NULL,
            password TEXT NOT NULL
        )
    ''')
    conn.commit()
    conn.close()

# Route to serve the login page
@app.route('/')
def home():
    with open("index.html", "r") as file:
        return file.read()

# Route to process form data
@app.route('/login', methods=['POST'])
def login():
    username = request.form['username']
    password = request.form['password']

    # Use the correct database path
    conn = sqlite3.connect(db_path)
    cursor = conn.cursor()

    cursor.execute("INSERT INTO Users (username, password) VALUES (?, ?)", (username, password))
    conn.commit()
    conn.close()

    return f"<h3>User '{username}' successfully logged in and stored in database.</h3>"

if __name__ == '__main__':
    app.run(debug=True)




    Your SQLite Databas


import sqlite3

# Connect to (or create) the database
conn = sqlite3.connect('login.db')
cursor = conn.cursor()

# Create users table
cursor.execute('''
CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL,
    password TEXT NOT NULL
)
''')

# Insert a sample user (optional)
cursor.execute('INSERT INTO users (username, password) VALUES (?, ?)', ('admin', 'admin123'))

conn.commit()
conn.close()


Python Script to Handle Login

import sqlite3

def login(username, password):
    conn = sqlite3.connect('login.db')
    cursor = conn.cursor()

    cursor.execute('SELECT * FROM users WHERE username=? AND password=?', (username, password))
    result = cursor.fetchone()

    conn.close()

    if result:
        return "Login successful!"
    else:
        return "Invalid username or password."

# Example usage:
uname = input("Enter username: ")
pwd = input("Enter password: ")

print(login(uname, pwd))



app.py)

from flask import Flask, request, render_template_string
import sqlite3

app = Flask(__name__)

def check_login(username, password):
    conn = sqlite3.connect('login.db')
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM users WHERE username=? AND password=?', (username, password))
    result = cursor.fetchone()
    conn.close()
    return result

@app.route('/', methods=['GET', 'POST'])
def login():
    msg = ''
    if request.method == 'POST':
        uname = request.form['username']
        pwd = request.form['password']
        if check_login(uname, pwd):
            msg = "Login successful!"
        else:
            msg = "Invalid credentials."
    return render_template_string('''
        <form method="post">
            <input type="text" name="username" placeholder="Username" required />
            <input type="password" name="password" placeholder="Password" required />
            <button type="submit">Login</button>
        </form>
        <p>{{msg}}</p>
    ''', msg=msg)

if __name__ == '__main__':
    app.run(debug=True)

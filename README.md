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

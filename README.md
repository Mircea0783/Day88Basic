# Day88Basic
Basic API python program

Use Pyton to create the next task " Build a website that lists cafes with wifi and power for remote working."
Here's a Python-based solution to create a website that lists cafes with WiFi and power for remote working, using Flask for the web framework and SQLite for the database.
python
# app.py
from flask import Flask, render_template, request, redirect, url_for
import sqlite3

app = Flask(__name__)

# Initialize database
def init_db():
    with sqlite3.connect('cafes.db') as conn:
        c = conn.cursor()
        c.execute('''CREATE TABLE IF NOT EXISTS cafes
                    (id INTEGER PRIMARY KEY AUTOINCREMENT,
                     name TEXT NOT NULL,
                     address TEXT NOT NULL,
                     wifi_speed TEXT,
                     power_outlets TEXT,
                     rating REAL)''')
        conn.commit()

# Home route - List all cafes
@app.route('/')
def index():
    with sqlite3.connect('cafes.db') as conn:
        conn.row_factory = sqlite3.Row
        c = conn.cursor()
        c.execute('SELECT * FROM cafes')
        cafes = c.fetchall()
    return render_template('index.html', cafes=cafes)

# Add cafe route
@app.route('/add', methods=['GET', 'POST'])
def add_cafe():
    if request.method == 'POST':
        name = request.form['name']
        address = request.form['address']
        wifi_speed = request.form['wifi_speed']
        power_outlets = request.form['power_outlets']
        rating = float(request.form['rating'])

        with sqlite3.connect('cafes.db') as conn:
            c = conn.cursor()
            c.execute('INSERT INTO cafes (name, address, wifi_speed, power_outlets, rating) VALUES (?, ?, ?, ?, ?)',
                     (name, address, wifi_speed, power_outlets, rating))
            conn.commit()
        return redirect(url_for('index'))
    return render_template('add_cafe.html')

# Run the app
if __name__ == '__main__':
    init_db()
    app.run(debug=True)
Create templates in a templates folder:
html
<!-- templates/index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Cafes for Remote Work</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .cafe { border: 1px solid #ccc; padding: 10px; margin: 10px 0; }
        .add-link { margin-bottom: 20px; }
    </style>
</head>
<body>
    <h1>Cafes with WiFi and Power</h1>
    <a href="{{ url_for('add_cafe') }}" class="add-link">Add New Cafe</a>
    
    {% for cafe in cafes %}
    <div class="cafe">
        <h3>{{ cafe['name'] }}</h3>
        <p>Address: {{ cafe['address'] }}</p>
        <p>WiFi Speed: {{ cafe['wifi_speed'] }}</p>
        <p>Power Outlets: {{ cafe['power_outlets'] }}</p>
        <p>Rating: {{ cafe['rating'] }}/5</p>
    </div>
    {% else %}
    <p>No cafes found. Add some!</p>
    {% endfor %}
</body>
</html>

<!-- templates/add_cafe.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Add Cafe</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        form { max-width: 500px; }
        label { display: block; margin: 10px 0 5px; }
        input, select { width: 100%; padding: 8px; margin-bottom: 10px; }
        button { padding: 10px 20px; background: #007bff; color: white; border: none; cursor: pointer; }
    </style>
</head>
<body>
    <h1>Add a Cafe</h1>
    <form method="POST">
        <label for="name">Cafe Name</label>
        <input type="text" id="name" name="name" required>
        
        <label for="address">Address</label>
        <input type="text" id="address" name="address" required>
        
        <label for="wifi_speed">WiFi Speed</label>
        <select id="wifi_speed" name="wifi_speed" required>
            <option value="Fast">Fast</option>
            <option value="Moderate">Moderate</option>
            <option value="Slow">Slow</option>
        </select>
        
        <label for="power_outlets">Power Outlets</label>
        <select id="power_outlets" name="power_outlets" required>
            <option value="Many">Many</option>
            <option value="Few">Few</option>
            <option value="None">None</option>
        </select>
        
        <label for="rating">Rating (0-5)</label>
        <input type="number" id="rating" name="rating" min="0" max="5" step="0.1" required>
        
        <button type="submit">Add Cafe</button>
    </form>
    <a href="{{ url_for('index') }}">Back to List</a>
</body>
</html>
To run this project:
1.Install Flask:
bash
pip install flask
2.Create a project directory with:
•app.py
•templates/ folder containing index.html and add_cafe.html
3.Run the application:
bash
python app.py
4.Access the website at http://127.0.0.1:5000
Features:
•Lists cafes with name, address, WiFi speed, power outlet availability, and rating
•Allows adding new cafes through a form
•Stores data in a SQLite database
•Simple, clean UI with basic styling
•Responsive form with validation
This is a minimal viable product. Potential enhancements could include:
•Search/filter functionality
•User authentication
•Cafe images
•Map integration
•User reviews
•Edit/delete cafe functionality

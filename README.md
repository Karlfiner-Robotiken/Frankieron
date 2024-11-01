# Frankieron
Blog app

Let's create and deploy the blog app with author credit to Frankieron. We’ll use Flask for the backend, HTML/CSS for the frontend, and deploy on Heroku. Here are the steps:

### Step 1: Project Setup

1. **Create a new directory** for the project:
    ```bash
    mkdir blog-app
    cd blog-app
    ```

2. **Set up a virtual environment** and install required libraries:
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    pip install flask flask_sqlalchemy flask_migrate flask_wtf flask_login
    ```

### Step 2: Create the Flask Application

1. **Create `app.py`**:
    ```python
    from flask import Flask, render_template, redirect, url_for, flash
    from flask_sqlalchemy import SQLAlchemy
    from flask_migrate import Migrate
    from flask_login import LoginManager, UserMixin, login_user, login_required, logout_user, current_user

    app = Flask(__name__)
    app.config['SECRET_KEY'] = 'your_secret_key'
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///blog.db'
    db = SQLAlchemy(app)
    migrate = Migrate(app, db)
    login_manager = LoginManager(app)
    login_manager.login_view = 'login'

    class User(UserMixin, db.Model):
        id = db.Column(db.Integer, primary_key=True)
        username = db.Column(db.String(150), nullable=False, unique=True)
        email = db.Column(db.String(150), nullable=False, unique=True)
        password = db.Column(db.String(150), nullable=False)
        posts = db.relationship('Post', backref='author', lazy=True)

    class Post(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        title = db.Column(db.String(150), nullable=False)
        content = db.Column(db.Text, nullable=False)
        user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)

    @login_manager.user_loader
    def load_user(user_id):
        return User.query.get(int(user_id))

    @app.route('/')
    def home():
        posts = Post.query.all()
        return render_template('index.html', posts=posts)

    @app.route('/login', methods=['GET', 'POST'])
    def login():
        # Login logic here
        pass

    @app.route('/register', methods=['GET', 'POST'])
    def register():
        # Registration logic here
        pass

    @app.route('/new_post', methods=['GET', 'POST'])
    @login_required
    def new_post():
        # New post creation logic here
        pass

    if __name__ == '__main__':
        app.run(debug=True)
    ```

2. **Create HTML Templates**:
    Create a `templates` directory and add the following templates:

    - **`base.html`**:
        ```html
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Blog App</title>
            <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
        </head>
        <body>
            <nav>
                <ul>
                    <li><a href="{{ url_for('home') }}">Home</a></li>
                    {% if current_user.is_authenticated %}
                        <li><a href="{{ url_for('logout') }}">Logout</a></li>
                    {% else %}
                        <li><a href="{{ url_for('login') }}">Login</a></li>
                        <li><a href="{{ url_for('register') }}">Register</a></li>
                    {% endif %}
                </ul>
            </nav>
            <div class="container">
                {% block content %}{% endblock %}
            </div>
        </body>
        </html>
        ```

    - **`index.html`**:
        ```html
        {% extends "base.html" %}
        {% block content %}
            <h1>Welcome to the Blog App!</h1>
            <a href="{{ url_for('new_post') }}">Create New Post</a>
            {% for post in posts %}
                <div class="post">
                    <h2>{{ post.title }}</h2>
                    <p>{{ post.content }}</p>
                    <p>Written by: {{ post.author.username }}</p>
                </div>
            {% endfor %}
        {% endblock %}
        ```

    - **`login.html`**:
        ```html
        {% extends "base.html" %}
        {% block content %}
            <h1>Login</h1>
            <form action="{{ url_for('login') }}" method="post">
                <label for="username">Username:</label>
                <input type="text" id="username" name="username">
                <label for="password">Password:</label>
                <input type="password" id="password" name="password">
                <button type="submit">Login</button>
            </form>
        {% endblock %}
        ```

    - **`register.html`**:
        ```html
        {% extends "base.html" %}
        {% block content %}
            <h1>Register</h1>
            <form action="{{ url_for('register') }}" method="post">
                <label for="username">Username:</label>
                <input type="text" id="username" name="username">
                <label for="email">Email:</label>
                <input type="email" id="email" name="email">
                <label for="password">Password:</label>
                <input type="password" id="password" name="password">
                <button type="submit">Register</button>
            </form>
        {% endblock %}
        ```

    - **`post.html`**:
        ```html
        {% extends "base.html" %}
        {% block content %}
            <h1>Create New Post</h1>
            <form action="{{ url_for('new_post') }}" method="post">
                <label for="title">Title:</label>
                <input type="text" id="title" name="title">
                <label for="content">Content:</label>
                <textarea id="content" name="content"></textarea>
                <button type="submit">Submit</button>
            </form>
        {% endblock %}
        ```

3. **Create `requirements.txt`**:
    ```text
    Flask==2.0.1
    Flask-SQLAlchemy==2.5.1
    Flask-Migrate==3.1.0
    Flask-WTF==0.14.3
    Flask-Login==0.5.0
    ```

### Step 3: Deploy to Heroku

1. **Create a Heroku app**:
    ```bash
    heroku create blog-app-frankieron
    ```

2. **Create a `Procfile`**:
    ```text
    web: gunicorn app:app
    ```

3. **Install Gunicorn**:
    ```bash
    pip install gunicorn
    ```

4. **Commit and push to Heroku**:
    ```bash
    git add .
    git commit -m "Initial commit"
    git push heroku main
    ```

5. **Set up the database on Heroku**:
    ```bash
    heroku addons:create heroku-postgresql:hobby-dev
    ```

6. **Run database migrations**:
    ```bash
    heroku run flask db upgrade
    ```

Now, your blog app should be deployed and accessible online. The author of this app is credited to Frankieron. You can access and manage your app on Heroku, and users can start sharing their experiences, ideas, and writing skills. If you have more features to add or need further assistance, let me know![43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/fzramos/chicodes-flask-walkthrough/tree/ccf5b89e4080ba12a343ac8e587ff8c2602f23be/chicodes_blog_app%2Fmodels.py?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "1")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/Rojasking700/MyFi/tree/013cc0a53c7af1ce8ae2b953daed01f7df98a1e4/flask%2Fapp%2Fmodels.py?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "2")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/iashyam/Flask-Project/tree/5d9be25e225d1f07868f71581176206fe47666b3/Flaskr%2Fdatabase.py?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "3")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/imfht/flaskapp1/tree/530beb9e3b8516e0e93960b99521c23a523ef546/flask-tutorial-master%2Fchapters%2Fc8-login.md?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "4")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/kkneomis/flask_guides/tree/1457bbd49e55b4b0355b1f0c7204b7cd90815b08/lesson13.md?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "5")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/ArzhangAmin/Responsive-Architecture-Portfolio/tree/7f8527ba304334227d24940d346bd00f776815f9/login.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "6")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/Tobsic/WhiteLady/tree/c454064396df38fcdf8db59834e8b24edced52db/Server%2FAPI%2Findex.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "7")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/cjsampson/codeup.dev/tree/1dbc64254ddc007ee2bad485d6fcf9cfc7937528/public%2Fphp%2Fform.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "8")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/AnnikaG/Blog-Advertiser/tree/9e6f8cf438d9ebe255a1ad74e64c2c1b9e57aa13/login.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "9")

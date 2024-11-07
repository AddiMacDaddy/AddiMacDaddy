from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from flask_mail import Mail, Message
import os
import random
import string

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = 'your_email@gmail.com'
app.config['MAIL_PASSWORD'] = 'your_email_password'
app.config['MAIL_DEFAULT_SENDER'] = 'your_email@gmail.com'

db = SQLAlchemy(app)
mail = Mail(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    password = db.Column(db.String(120), nullable=False)

def generate_password(length=8):
    characters = string.ascii_letters + string.digits
    return ''.join(random.choice(characters) for i in range(length))

@app.route('/register', methods=['POST'])
def register():
    data = request.json
    username = data.get('username')
    if User.query.filter_by(username=username).first():
        return jsonify({'message': 'Username already exists'}), 400
    
    password = generate_password()
    new_user = User(username=username, password=password)
    db.session.add(new_user)
    db.session.commit()

    msg = Message('Welcome to Our Social Media Platform', recipients=[data.get('email')])
    msg.body = f'Your account has been created.\nUsername: {username}\nPassword: {password}'
    mail.send(msg)

    return jsonify({'message': 'User registered successfully, check your email for credentials'}), 201
password = generate_password()
new_user = User(username=username, password=password)
db.session.add(new_user)
db.session.commit()

msg = Message('Welcome to Our Social Media Platform', recipients=[data.get('email')])
msg.body = f'Your account has been created.\nUsername: {username}\nPassword: {password}'
mail.send(msg)

return jsonify({'message': 'User registered successfully, check your email for credentials'}), 201

if __name__ == '__main__':
    db.create_all()
    app.run(debug=True)

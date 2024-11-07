import hashlib

class SocialPlatform:
    def __init__(self, name, admin_username, admin_password):
        self.name = name
        self.admin_username = admin_username
        self.admin_password = self.hash_password(admin_password)
        self.users = []

    def hash_password(self, password):
        return hashlib.sha256(password.encode()).hexdigest()

    def add_user(self, username, password):
        hashed_password = self.hash_password(password)
        self.users.append({'username': username, 'password': hashed_password})

    def authenticate_admin(self, username, password):
        return (username == self.admin_username and 
                self.hash_password(password) == self.admin_password)

platform = SocialPlatform("AIRecord")

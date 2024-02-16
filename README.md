# SMS-Magic-Assignment

Python
pip install Flask
from flask import Flask, request, jsonify
from datetime import datetime

app = Flask(__name__)

#creating the apis of users,companies,clients
users = []
companies = []
clients = []

# Entities 
class User:
    def __init__(self, username, email):
        self.username = username
        self.email = email

class Company:
    def __init__(self, name):
        self.name = name
        self.users = []

class ClientUser:
    def __init__(self, client, user):
        self.client = client
        self.user = user
        self.createdAt = datetime.now()
        self.updatedAt = datetime.now()
        self.deletedAt = None
        self.active = True

class Client:
    def __init__(self, name, user, company, email, phone):
        self.name = name
        self.user = user
        self.company = company
        self.email = email
        self.phone = phone


@app.route('/users', methods=['GET'])
def list_users():
    username = request.args.get('username')
    if username:
        filtered_users = [user for user in users if user.username == username]
        return jsonify([user.__dict__ for user in filtered_users])
    return jsonify([user.__dict__ for user in users])

@app.route('/users/<username>', methods=['PUT'])
def replace_user(username):
    
    data = request.json
    for user in users:
        if user.username == username:
            user.username = data['username']
            user.email = data['email']
            return jsonify(user.__dict__)
    return jsonify({'error': 'User not found'})

@app.route('/clients', methods=['POST'])
def create_client():
    data = request.json
    
    existing_companies = [client.company.name for client in clients]
    if data['company'] in existing_companies:
        return jsonify({'error': 'Company already taken by another client'})
    user = User(data['user']['username'], data['user']['email'])
    company = Company(data['company'])
    client = Client(data['name'], user, company, data['email'], data['phone'])
    clients.append(client)
    companies.append(company)
    users.append(user)
    return jsonify(client.__dict__)

@app.route('/clients/<client_id>', methods=['PATCH'])
def change_client_field(client_id):
    data = request.json
    for client in clients:
        if client.name == client_id:
            for key, value in data.items():
                setattr(client, key, value)
            return jsonify(client.__dict__)
    return jsonify({'error': 'Client not found'})

if __name__ == '__main__':
    app.run(debug=True)


    SQL
    Search for Companies by employees range.
    SELECT * FROM Companies WHERE employees BETWEEN 500 AND 2000;

    Search for Clients by:
●   User: i.e. retrieve all Clients a User belongs to
●   Name: i.e. search within the company's name text
SELECT * FROM Clients WHERE user_id = :user_id;
SELECT * FROM Clients WHERE name LIKE '%:search_term%';

Using one raw SQL:
●   get a list of companies where each company has max revenue in its industry: e.g. Amazon for E-commerce
SELECT c1.* FROM Companies c1 
LEFT JOIN Companies c2 ON c1.industry = c2.industry AND c1.revenue < c2.revenue 
WHERE c2.id IS NULL;

4 SECURITY
4.1 Secure the Create Client endpoint (2.3) to allow only ROLE_ADMIN users to use it
implement role-based access control (RBAC)
Add some regex
●  add some regex to validate emails on GET /user/profile endpoint.
^[\w\.-]+@[a-zA-Z\d\.-]+\.[a-zA-Z]{2,}$

STEP - 7 Documenting
GET /users: Retrieve a list of users. Supports filtering by username.
PUT /users/{username}: Replace user fields. Update all fields at once.
POST /clients: Create a client. Access restricted to ROLE_ADMIN users.
PATCH /clients/{client_id}: Change client field(s). Update any single or multiple fields at once.
SQL Queries: Custom SQL functions for searching companies and clients, as well as retrieving companies with max revenue in each industry.
Security: Endpoint access control restricted to ROLE_ADMIN users.
Regex: Regex pattern for email validation.
Tests: Assertion tests for various scenarios.


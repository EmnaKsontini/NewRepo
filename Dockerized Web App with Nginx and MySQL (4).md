
# Lab 1

## Introduction
In this lab, you will explore the fundamentals of RESTful APIs, including how to develop your own API, effectively call external APIs, and utilize OAuth for secure authentication. Additionally, the lab will cover the practical use of webhooks, focusing particularly on their application with the GitHub API.

## FastAPI Introduction
### what is FastAPI?

FastAPI is a Python framework used for creating web applications and APIs (Application Programming Interfaces). It's called "FastAPI" because it allows you to build APIs quickly and efficiently.

### Why Use FastAPI?

- **Speed:** FastAPI is built on Starlette for the web part and Pydantic for the data part, making it very fast.
- **Easy to Use:** It has an intuitive design, which makes it easy to learn, especially for those already familiar with Python.
- **Automatic Documentation:** One of the coolest features of FastAPI is that it automatically generates documentation for your API. This means you can see and test your API endpoints easily.
- **Data Validation and Serialization:** FastAPI simplifies data validation and serialization. It uses modern Python type hints, making your code more robust and clear.


### How Does FastAPI Work?

- You define path operations for your API. These are the different requests you can make, like GET, POST, PUT, and DELETE.
Path parameters and query parameters are used to define variable parts of your routes and queries.
- For data coming into your API, you define Pydantic models, which help with data validation and serialization.

## REST API Overview

REST, short for Representational State Transfer, is an architectural style for creating web services that operate via the HTTP protocol. Its principles were established in 2000 by computer scientist Roy Fielding, and it has since become a preferred method due to its scalability and flexibility, outpacing older techniques of machine-to-machine communication. To this day, it remains the de facto standard for public APIs.

## REST API Concepts

At the heart of the REST API design are the following elements:

- **Requests and Responses**: To access a resource, the client sends an HTTP request. The server then responds with an HTTP response, containing the encoded data about the resource. Both the request and response are self-descriptive, carrying the necessary details to parse and handle them.

### Key Components of a REST Request:

Every REST request is composed of four critical elements:

1. **HTTP Method**: This specifies the action to be performed on the resource. The four basic methods, also known as CRUD operations, are:
   - `GET`: Retrieve a resource.
   - `POST`: Create a new resource.
   - `PUT`: Update an existing resource.
   - `DELETE`: Delete a resource.

2. **Endpoint**: The endpoint includes a Uniform Resource Identifier (URI), which specifies where the resource can be found on the Internet. The most common URI form is a Uniform Resource Locator (URL), which is the full address used to access the web resource.

3. **Headers**: These contain metadata for the HTTP request and response. Headers are used for various purposes, such as providing authentication (e.g., API keys), specifying the host (server name or IP address), and indicating the desired response format.

4. **Body**: The body of the request carries additional information to the server. This could be data that you wish to create or update.




---

### Step 0: Setting Up Your Environment

1. **Install Python**: Make sure Python 3.6+ is installed on your system. You can download it from the official Python website.
2. **Install FastAPI**: Run `pip install fastapi` in your terminal.
3. **Install Uvicorn**: Uvicorn is an ASGI server. Install it using `pip install uvicorn`.

### Step 1 : Project Setup

1. **Create a New Project Folder**: 

    Create a new folder named `Repo_management_app` for your project.

    ```bash
    mkdir Repo_management_app
    cd Repo_management_app
    ```

2. **Create the Main Application File**:

    Inside the `Repo_management_app` folder, create a file named `main.py`. This file will serve as the entry point for our FastAPI application.

    ```python
    from fastapi import FastAPI
    import uvicorn

    app = FastAPI()

    @app.get("/")
    def read_root():
        return {"Message": "Repo Management Tool API"}
    
    if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8080)

    ```

3. **Navigate to Your Project Directory**:

    Open your terminal and navigate to the `Repo_management_app` directory.

2. **Start the FastAPI Application**:

    Run the application using Uvicorn with the following command:

    ```bash
    uvicorn main:app --reload
    ```

     The `--reload` flag is particularly useful as it enables auto-reload. This means that the server will automatically restart whenever you make changes to the code. **You won't need to manually stop and restart the server each time you update your code**, which greatly facilitates the development process.
3. **Verify the Application**:

    Open your browser and go to [http://127.0.0.1:8000/](http://127.0.0.1:8000/). You should see the JSON response ` {"Message": "Repo Management Tool API"}`.



### Step 2: Repo Management App
We are going to create a FastAPI application, named "Repo Management App", which is designed to simulate the management of software repositories and user accounts in a straightforward and efficient manner. The application features a series of endpoints to handle user registrations, repository additions, data retrievals, and updates. 

#### 2.1. Update `main.py`:
  ```python
  from fastapi import FastAPI, HTTPException, Query, Path
  from typing import Optional
  import uvicorn

  app = FastAPI()

  # In-memory storage for demonstration purposes (we coud have used Database for storing data but for simplicty we will use in-memory)
  
  repo_data = {} 
  user_data = {}  
      
  if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
  ```
  
**🔴 Very Important Note:**

When working with the `main.py` file in our FastAPI application, it's crucial to be aware of the following:

Even though we've made updates to the content of `main.py`, there's no need to manually re-run the command:

```bash
uvicorn main:app --reload
```

This is because we're using the `--reload` option with `uvicorn`, which enables automatic reloading of the server whenever changes are detected in the code.

However, it's important to remember that our current setup uses in-memory storage for data. This means that **every time the server automatically restarts due to code changes, all stored data will be lost**. Therefore, any modifications to `main.py` that trigger a server restart will result in a reset of our in-memory data.
#### 2.2. Models Creation:
Pydantic is a data validation and settings management library that is integral to FastAPI. In FastAPI, Pydantic models are used to define the structure, types, and validation rules for data that your API receives and sends. 

When you define your API endpoints in FastAPI, you typically use Pydantic models to specify the expected request body and the format of the response data. FastAPI uses these models to:

- Validate incoming JSON requests to ensure they match the schema defined by the Pydantic model.
- Serialize model instances to JSON responses automatically.
- Generate OpenAPI schemas for your API documentation, which is one of FastAPI's standout features.

In this App we will be using the following

- **`User` Model**: This Pydantic model represents a user with `username` and `email` fields. It's used in the user registration process to validate and store user information.
- **`RepoData` Model**: Another Pydantic model that encapsulates repository information, including `name` and an optional `description`. This model is used for updating and storing repository data.


1. **Add the following Models to your  `main.py`**:
 ```python
  from pydantic import BaseModel

  class User(BaseModel):
      username: str
      email: str
      
  class RepoData(BaseModel):
      name: str
      description: Optional[str] = None

  ```
  
  
#### 2.3 Creating Endpoints
These endpoints cover the basic operations of user registration, repository management (addition, retrieval, update, and deletion), and provide a simple root message. They use HTTP status codes to indicate the success or failure of the requests, following standard RESTful practices. Add the following endpoints to the a :

1. **Root Endpoint**
   - **HTTP Method:** GET
   - **Endpoint:** `/`
   - **Description:** This endpoint provides a root message. It doesn't require any parameters or a request body.
   - **Code Snippet:**
     ```python
     @app.get("/")
     def read_root():
         return {"Message": "GitHub Analytics Tool API"}
     ```
   - **Status Codes:** 
     - 200 OK: Successfully retrieved the root message.

2. **Register User**
   - **HTTP Method:** POST
   - **Endpoint:** `/register`
   - **Path Parameters:** None
   - **Query Parameters:** None
   - **Expected Body:** JSON with `username` and `email`.
   - **Description:** Registers a new user.
   - **Status Codes:** 
     - 201 Created: User registered successfully.
     - 400 Bad Request: Username already exists.
     - **Code Snippet:**
     ```python
     @app.post("/register", status_code=201)
     def register_user(user: User):
         if user.username in user_data:
             raise HTTPException(status_code=400, detail="Username already exists")
         user_data[user.username] = {"email": user.email, "repos": []}
         return {"Message": "User registered successfully"}
     ```


3. **Add Repository**
   - **HTTP Method:** POST
   - **Endpoint:** `/user/{username}/add_repo`
   - **Path Parameters:** 
     - `username`: The username to which the repository is to be added.
   - **Query Parameters:** None
   - **Expected Body:** JSON with repository `name` and optional `description`.
   - **Description:** Adds a new repository to a specified user.
   - **Status Codes:** 
     - 200 OK: Repository added successfully.
     - 404 Not Found: User not found.
     - 400 Bad Request: Repository name already exists.
   - **Code Snippet:**
     ```python
     @app.post("/user/{username}/add_repo")
     def add_repository(username: str, repo: RepoData):
         if username not in user_data:
             raise HTTPException(status_code=404, detail="User not found")
         if repo.name in repo_data:
             raise HTTPException(status_code=400, detail="Repository name already exists")
         repo_data[repo.name] = repo.dict()
         user_data[username]["repos"].append(repo.name)
         return {"Message": "Repository added successfully"}
     ```

4. **Get Repository**
   - **HTTP Method:** GET
   - **Endpoint:** `/repo/{repo_name}`
   - **Path Parameters:** 
     - `repo_name`: The name of the repository to retrieve.
   - **Query Parameters:** None
   - **Expected Body:** None
   - **Description:** Retrieves information about a specific repository.
   - **Status Codes:** 
     - 200 OK: Successfully retrieved the repository data.
     - 404 Not Found: Repository not found.
   - **Code Snippet:**
     ```python
     @app.get("/repo/{repo_name}")
     def get_repository(repo_name: str = Path(...)):
         if repo_name not in repo_data:
             raise HTTPException(status_code=404, detail="Repository not found")
         return repo_data[repo_name]
     ```


5. **Update Repository**
   - **HTTP Method:** PUT
   - **Endpoint:** `/update_repo/{repo_name}`
   - **Path Parameters:** 
     - `repo_name`: The name of the repository to update.
   - **Query Parameters:** None
   - **Expected Body:** JSON with updated repository `name` and optional `description`.
   - **Description:** Updates the data of an existing repository.
   - **Status Codes:** 
     - 200 OK: Repository data updated successfully.
     - 404 Not Found: Repository not found.
   - **Code Snippet:**
     ```python
     @app.put("/update_repo/{repo_name}")
     def update_repository(repo_name: str, data: RepoData):
         if repo_name not in repo_data:
             raise HTTPException(status_code=404, detail="Repository not found")
         repo_data[repo_name] = data.dict()
         return {"Message": "Repository data updated successfully"}
     ```
  
6. **Delete Repository**
   - **HTTP Method:** DELETE
   - **Endpoint:** `/user/{username}/delete_repo`
   - **Path Parameters:** 
     - `username`: The username from whose account the repository is to be deleted.
   - **Query Parameters:**
     - `repo_name`: The name of the repository to delete.
   - **Expected Body:** None
   - **Description:** Deletes a repository from a specified user's account.
   - **Status Codes:** 
     - 200 OK: Repository deleted successfully.
     - 404 Not Found: User not found or repository not found in user account.
   - **Code Snippet:**
     ```python
     @app.delete("/user/{username}/delete_repo")
     def delete_repository(username: str, repo_name: str = Query(...)):
         if username not in user_data:
             raise HTTPException(status_code=404, detail="User not found")
         if repo_name not in user_data[username]["repos"]:
             raise HTTPException(status_code=404, detail="Repository not found in user account")
         user_data[username]["repos"].remove(repo_name)
         return {"Message": "Repository deleted successfully"}
     ```
  
Hints: 
   - Path parameters are part of the URL path of the request. They are used to identify a specific resource or resources. 
   - Path parameters are included in the route definition. For example, in `/user/{username}`, `username` is a path parameter.
   - Query parameters, on the other hand, are appended to the URL with a '?' and can be used to filter or sort the data, or specify certain options.For example, in `/items?sort=asc&page=2`, `sort` and `page` are query parameters.
 

#### 2.4 Testing the Endpoints
- Use a tool like Postman https://www.postman.com/downloads/  to test these endpoints and make sure each endpoint handles requests and responses correctly. 
- Add an endpoint to display list of all users and to update a user email explain your endpoints.

### Step 3: Github Repo Management App using Github API App

 Moving beyond the limitations of managing fictional repositories internally, our application has now evolved to integrate directly with GitHub's powerful API. This enhancement allows users to directly manage their GitHub repositories through our application.
 
 
#### 3.1  Github Repo Management App Registration

To utilize the GitHub API, it's essential to first register our application on GitHub. This registration process grants us a unique Client ID and Client Secret. 

please go to https://github.com/settings/applications/new fill the app info (you need to have a github account to accomplish this).



Generate a client secret and copy them somwhere along with the client ID.


#### 3.2 Repo Management OAuth and endpoints implementation
1- `httpx` is an HTTP client library for Python, supporting both synchronous and asynchronous requests, we will need it for this step, please install it with pip using the command `pip install httpx`.

2- Delete the content of `main.py` and replace it with the following:

```python
from fastapi import FastAPI, HTTPException, Query, Request
from pydantic import BaseModel
from typing import Optional
import httpx
from fastapi.responses import RedirectResponse

app = FastAPI()

repo_data = {}  # Stores repository data indexed by repo ID
user_data = {}  # Stores user data indexed by username

class User(BaseModel):
    username: str
    email: str

class RepoData(BaseModel):
    name: str
    description: Optional[str] = None
    homepage: Optional[str] = None
    private: bool
    has_issues: Optional[bool] = False
    has_projects: Optional[bool] = False
    has_wiki: Optional[bool] = False
    is_template: Optional[bool] = True

async def github_api_request(url: str, token: str):
    headers = {"Authorization": f"Bearer {token}"}
    async with httpx.AsyncClient() as client:
        response = await client.get(url, headers=headers)
    return response.json()

CLIENT_ID = "your_client_id"
CLIENT_SECRET = "your_client_secret"

@app.get("/")
def read_root():
    return {"Message": "Github Repo Management App"}

@app.get("/auth/login")
def login():
    github_auth_url = (
        f"https://github.com/login/oauth/authorize"
        f"?client_id={CLIENT_ID}"
        f"&redirect_uri=http://localhost:8000/auth/callback"
        f"&scope=repo"
    )
    return RedirectResponse(url=github_auth_url)

@app.get("/auth/callback")
async def auth_callback(code: str):
    token_url = "https://github.com/login/oauth/access_token"
    async with httpx.AsyncClient() as client:
        token_response = await client.post(
            token_url,
            headers={"Accept": "application/json"},
            data={
                "client_id": CLIENT_ID,
                "client_secret": CLIENT_SECRET,
                "code": code
            }
        )
    access_token = token_response.json().get("access_token")

    user_data_response = await github_api_request("https://api.github.com/user", access_token)
    username = user_data_response.get("login")

    if username in user_data:
        raise HTTPException(status_code=400, detail="Username already exists")

    user_data[username] = {
        "email": user_data_response.get("email"), 
        "repos": [], 
        "token": access_token
    }
    return {"Message": "User authenticated and data retrieved successfully", "username": username}

@app.post("/user/{username}/add_repo")
async def add_repository(username: str, repo_data: RepoData):
    if username not in user_data or "token" not in user_data[username]:
        raise HTTPException(status_code=401, detail="User not authenticated or token missing")

    token = user_data[username]["token"]
    headers = {
        "Authorization": f"Bearer {token}",
        "Accept": "application/vnd.github.v3+json"
    }
    data = repo_data.dict()
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "https://api.github.com/user/repos",
            headers=headers,
            json=data
        )

    if response.status_code != 201:
        raise HTTPException(status_code=response.status_code, detail=response.text)

    response_data = response.json()
    repo_id = response_data.get("id")
    user_data[username]["repos"].append(repo_id)
    return {"Message": "Repository added successfully", "repo_info": response_data}

@app.get("/repo/{repo_id}")
async def get_repository(repo_id: str, username: str = Query(...)):
    if username not in user_data or "token" not in user_data[username]:
        raise HTTPException(status_code=401, detail="User not authenticated or token missing")

    token = user_data[username]["token"]
    repo_info = await github_api_request(f"https://api.github.com/repos/{username}/{repo_id}", token)

    return repo_info
```

#### Description

1. **Imports & App Initialization**: Import necessary modules and initialize the FastAPI application.
2. **Data Models**: Define Pydantic models (`User` and `RepoData`) for validating and structuring user and repository data.
3. **Helper Function**: An asynchronous function `github_api_request` for making authenticated requests to GitHub's API.
4. **OAuth Credentials & Endpoints**:
    - Define GitHub OAuth credentials (client ID and secret).
    - Root endpoint (`/`) for a simple API check.
    - `/auth/login` for initiating GitHub OAuth.
    - `/auth/callback` for handling the OAuth callback










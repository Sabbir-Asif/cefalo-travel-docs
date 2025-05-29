# Cefalo Travel Connect

## Module 1: Auth & User Management
- User can register with email and password and name
- User can log in with email and password
- User can reset password
- User can update profile information

User entity have the following properties:
  - id: int
  - email: string (unique)
  - password: string (hashed)
  - role: enum (admin, traveler, explorer)
  - profile_image: string (URL to profile image)
  - bio: string (short bio of the user)
  - created_at: datetime
  - updated_at: datetime

### 1.1 Signup:

api: POST /auth/signup

- User can register with email, password, and name
- Every user will initially have the role EXPLORER
- Other field remains null

### 1.2 Login:

api: POST /auth/login

- User can log in with email and password
- You have to give user and token in response

### 1.3 Reset Password:

api: POST /auth/reset-password

- Will be added soon.

### 1.4 Update Profile:

api: PUT /users/id

- User can update any attribute except email

## Module 2: Social Sharing

### 2.1 Creating a blog

api: POST /blogs

Entity: Blog
  - id: int
  - title: string
  - userId: int (foreign key to User)
  - locationName: string
  - locationPoints: (latitude and longitude)
  - description: string (text description of the blog)
  - cover_image: string
  - status: enum (draft, published, archived)
  - tags: list of strings
  - images: list of strings (URLs)
  - videos: list of strings (URLs)
  - transportDetails: [transport]
  - lodgeDetails: [lodge]
  - recomendations: [recommendation]
  - created_at: datetime
  - updated_at: datetime

- User creates a blog by providing
    - title
    - locationName
    - locationPoints
    (search by city or location name you get the lattitude and longitude and use postgis)
    - description
    - tags (list of strings)

- You add status by default as DRAFT

### 2.2 Update the blog content

api: PUT /blogs/id

- Users adds a cover image
- Users can add images and videos
- Users change the status

### 2.3 Adding Transport Information

Entity: Transport
  - id: int
  - type: string (bus, train, flight)
  - name: string
  - startingLocationName: string
  - startingPoint: latitude and longitude
  - destinationLocationName: string
  - destinationPoint: latitude and longitude
  - departure_time: datetime
  - fare: float

api: 
1. GET /transports/startingLocationNames
2. GET /transports/destinationLocationNames
3. GET /transports?startingLocationName={startingLocationName}&destinationLocationName={destinationLocationName}&type={type} (type is bus, train, flight, boat)
4. POST /blogs/transports (passes blogId and transportId in request body)
5. POST /transports (creates a new transport)
6. GET /blogs/:blogId/transports (gets all transports for a blog)

- Users add transport information
User first gives:
    - startingPoint and destination and type (bus, train, flight, boat)
You search for the transport by the location and type
    - If found you show a list of transport with fare and time
        - User selects one of them and clicks a button "Add Transport"
        - You add the transportId and userId to the BlogTransport table
    - If not found you show a button "create new transport"
        - User clicks the button and you show a form to add transport
        - User fills the form and clicks "Add Transport"
        - You add the transport to the database and return the transport data and show it in a card
        - User clicks "Add Transport" and you add the transportId and userId to the BlogTransport table
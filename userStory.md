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
        - User fills the form and clicks "submit"
        - You add the transport to the database and return the transport data and show it in a card
        - User clicks "Add Transport" and you add the transportId and userId to the BlogTransport table

### 2.4 Adding Lodge Information

Entity: Lodge
  - id: int
  - name: string (name of the lodge)
  - locationName: string
  - locationPoints: (latitude and longitude)
  - price_per_night: number
  - description: string (text description of the lodge)
  - images: list of strings (URLs)

api:
1. GET /lodges/locationNames
2. GET /lodges?locationName={locationName}
3. POST /blogs/lodges (passes blogId and lodgeId in request body)
4. POST /lodges (creates a new lodge)
5. GET /blogs/:blogId/lodges (gets all lodges for a blog)

- Users add lodge information
User first gives:
    - locationName
You search for the lodge by the location
    - If found you show a list of lodges with price and description
        - User selects one of them and clicks a button "Add Lodge"
        - You add the lodgeId and userId to the BlogLodge table
    - If not found you show a button "create new lodge"
        - User clicks the button and you show a form to add lodge
        - User fills the form and clicks "Submit"
        - You add the lodge to the database and return the lodge data and show it in a card
        - User clicks "Add Lodge" and you add the lodgeId and userId to the BlogLodge table

### 2.5 Adding Recommendation Information

Entity: Recommendation
  - id: int
  - blogId: int (foreign key to Blog)
  - label: string (food, activity, place)
  - data: string

api:
1. GET /blogs/:blogId/recommendations
2. POST /blogs/:blogId/recommendations (creates a new recommendation)
3. DELETE /blogs/:blogId/recommendations/:recommendationId (deletes a recommendation)



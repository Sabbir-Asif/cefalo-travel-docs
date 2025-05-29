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
7. DELETE /blogs/:blogId/transports/:transportId (deletes a transport from the blog)

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
6. DELETE /blogs/:blogId/lodges/:lodgeId (deletes a lodge from the blog)

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


## Module 2: Wishlist Management

### 2.1 Creating a wishlist entity

Entity: Wishlist
  - id: int
  - locationName: string
  - estimated_travel_date: datetime
  - tags: list of strings
  - note: string
  - coverImage: string (URL to cover image)
  - userId: int (foreign key to User)

api: 
1. POST /wishlists
2. GET /wishlists?userId={userId}
3. GET /wishlists/:id
4. PUT /wishlists/:id
5. DELETE /wishlists/:id

### 2.2 Adding a blog to the wishlist

Table: WishlistBlog
- userId: int (foreign key to User)
- blogId: int (foreign key to Wishlist)

api: 
1. POST /wishlist-blogs
2. GET /wishlist-blogs?userId={userId}
3. GET /wishlist-blogs/:id
4. DELETE /wishlist-blogs/:id

## Module 3: Creating a Trip

Entity: Trip
  - id: int
  - plannerId: int (foreign key to User)
  - title: string
  - description: string
  - startDate: datetime
  - endDate: datetime
  - coverImage: string (URL to cover image)
  - groupChatId: int (foreign key to GroupChat) (default null)
  - status: enum (in-progress, completed)
  - created_at: datetime
  - updated_at: datetime

api:
1. POST /trips
2. GET /trips?plannerId={plannerId}
3. GET /trips/:id
4. PUT /trips/:id
5. DELETE /trips/:id

### 3.1 Adding members to trip

Table: TripMember
- tripId: int (foreign key to Trip)
- userId: int (foreign key to User)

api:
1. POST /trip-members
2. GET /trip-members?tripId={tripId}
3. GET /trip-members/:id
4. DELETE /trip-members/:id

### 3.2 Matchmaking

api:
1. GET /matches?locationName={locationName}&travelDate={travelDate}
(returns users who has wishlist with the same location)
2. POST /trip-requests (creates a new trip request)
3. GET /trip-requests?senderId={senderId}&receiverId={receiverId}
4. DELETE /trip-requests/:id

Entity: TripRequest
  - id: int
  - tripId: int (foreign key to Trip)
  - senderId: int (foreign key to User)
  - receiverId: int (foreign key to User)
  - message: string
  - status: enum (pending, accepted, rejected)

### 3.3 Add transport to trip

Entity: TripTransport
  - id: int
  - tripId: int (foreign key to Trip)
  - transportId: int (foreign key to Transport)

api:
1. GET /transports/startingLocationNames
2. GET /transports/destinationLocationNames
3. GET /transports?startingLocationName={startingLocationName}&destinationLocationName={destinationLocationName}&type={type}
4. POST /trips/transports (passes tripId and transportId in request body)
5. POST /transports (creates a new transport)
6. GET /trips/:tripId/transports (gets all transports for a trip)
7. DELETE /trips/:tripId/transports/:transportId (deletes a transport from the trip)

Note: Follow the same logic as in Module 2.3 for adding transport information.

### 3.4 Add lodge to trip
Entity: TripLodge
  - id: int
  - tripId: int (foreign key to Trip)
  - lodgeId: int (foreign key to Lodge)

api:
1. GET /lodges/locationNames
2. GET /lodges?locationName={locationName}
3. POST /trips/lodges (passes tripId and lodgeId in request body)
4. POST /lodges (creates a new lodge)
5. GET /trips/:tripId/lodges (gets all lodges for a trip)
6. DELETE /trips/:tripId/lodges/:lodgeId (deletes a lodge from the trip)

### 3.5 Group Chat

Trip planner creates a group chat for the trip

Entity: GroupChat
  - id: int
  - creatorId: int (foreign key to User)
  - tripId: int (foreign key to Trip)
  - name: string
  - created_at: datetime

api:
1. POST /chats (creates a new group chat)
2. GET /chats/:id
3. PUT /chats/:id
4. DELETE /chats/:id

#### 3.5.1 messages to chat

api:
1. POST /chats/:chatId/messages (creates a new message in the group chat)
2. GET /chats/:chatId/messages (gets all messages in the group chat)

Entity: Message
  - id: int
  - groupChatId: int (foreign key to GroupChat)
  - userId: int (foreign key to User)
  - content: string (text content of the message)
  - timestamp: datetime


# Ceflao Travel Connect

## Module1: Social Sharing

- User can publish a blog with images and videos
- User can add location and tags
- User can add a title and description
- User can add a cover image

Entity: User
Attributes:
  - id: int
  - email: string (unique)
  - password: string (hashed)
  - role: enum (admin, traveler, explorer)
  - profile_image: string (URL to profile image)
  - bio: string (short bio of the user)
  - created_at: datetime
  - updated_at: datetime

Entity: Blog
Attributes:
  - id: int
  - title: string
  - userId: int (foreign key to User)
  - location: string (search by city or location name)
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


Transport:
Attributes:
  - id: int
  - type: string (bus, train, flight)
  - name: string (name of the transport)  (user will have suggestions)
  - startingPoint: location
  - destination: location
  - departure_time: datetime
  - arrival_time: datetime (optional)
  - Fare: float  (user will have suggestions for fare from previous data)

Lodge:
Attributes:
  - id: int
  - name: string (name of the lodge)
  - location: location
  - price_per_night: float
  - description: string (text description of the lodge)
  - images: list of strings (URLs)

Recommendation:
Attributes:
  - id: int
  - label: string (food, activity, place)
  - data: [string] (comma seperated list of strings)

Table: BlogTransport
Attributes:
  - blogId: int (foreign key to Blog)
  - transportId: int (foreign key to Transport)

Table: BlogLodge
Attributes:
  - blogId: int (foreign key to Blog)
  - lodgeId: int (foreign key to Lodge)

Table: BlogRecommendation
Attributes:
  - blogId: int (foreign key to Blog)
  - recommendationId: int (foreign key to Recommendation)

## Module2:  Wishlist Management
- User can add a blog to their wishlist
- we extract location and tags from the blog
- User add estimated travel date
- User can add a note

Entity: Wishlist
Attributes:
  - id: int
  - location: location (from the blog or search by city or location name)
  - estimated_travel_date: datetime
  - tags: list of strings
  - note: string
  - userId: int (foreign key to User)
  - blog_id: int (foreign key to Blog) (optional)
  - promo: string (url of any external blog or video) (optional)
  - created_at: datetime

## Module3:  Matchmaking and Planning
- A dashboard with cards a user's wishlist
- a list of users with common interest
- Plan a trip 
  - Enters location (suggestion from wishlist)
  - trip starting date
  - trip ending date
- User saves the trip it shows as card then opening that card planner can create a group
  - create a group
    - List of users from wishlist matching by the location and time
    - Planner can search by any username or email
    - select all options or select individual
    - then sent trip request
    - if accept then add him to the group
- With tour members a group chat is created automatically

Entity: Trip
Attributes:
  - id: int
  - title: string
  - starting_point: location {name, lat, long}
  - destination: location {name, lat, long}
  - description: string (text description of the blog)
  - transportDetails: [transport]
  - lodgeDetails: [lodge]
  - ChatGroupId: int (foreign key to GroupChat)
  - members: [users]
  - notification_marker: [location]
  - status: (upcomming, ongoing, completed)
  - created_at: datetime
  - updated_at: datetime


Table: TripMembers
Attributes:
  - tripId: int (foreign key to Trip)
  - userId: int (foreign key to User)

Entity: TripRequest
Attributes:
  - id: int
  - tripId: int
  - userFrom: user
  - userTo: user
  - message: string
  - status (pending, accepted) (not rejected because it rejects we will delete it)


### submodule 3.1 Group Chat
Entity: GroupChat
Attributes:
  - id: int
  - name: string
  - members: [int] (list of user IDs)
  - created_at: datetime
  - updated_at: datetime

Entity: Message
Attributes:
  - id: int
  - groupChatId: int (foreign key to GroupChat)
  - userId: int (foreign key to User)
  - content: string (text content of the message)
  - timestamp: datetime
  - media: list of strings (URLs for images or videos, optional)

Table: GroupChatMembers
Attributes:
  - groupChatId: int (foreign key to GroupChat)
  - userId: int (foreign key to User)

### submodule 3.2 Proximity Notification
Entity: Location
Attributes:
  - id: int
  - name: string
  - lat: float
  - long: float
  - isComplete: boolean




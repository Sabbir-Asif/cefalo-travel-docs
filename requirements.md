# Ceflao Travel Connect

## Module1: Social Sharing

- User can publish a blog with images and videos
- User can add location and tags
- User can add a title and description
- User can add a cover image

Entity: Blog
Attributes:
  - id: int
  - title: string
  - location: string (search by city or location name)
  - description: string (text description of the blog)
  - cover_image: string
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
  - starting_point: string (starting point of the transport)
  - destination: string (destination of the transport)
  - departure_time: datetime
  - arrival_time: datetime (optional)
  - Fare: float  (user will have suggestions for fare from previous data)

Lodge:
Attributes:
  - id: int
  - name: string (name of the lodge)
  - location: string (map url)
  - price_per_night: float
  - description: string (text description of the lodge)
  - images: list of strings (URLs)

Recommendation:
Attributes:
  - id: int
  - label: string (food, activity, place)
  - data: [string] (comma seperated list of strings)

## Module2:  Wishlist Management
- User can add a blog to their wishlist
- we extract location and tags from the blog
- User add estimated travel date
- User can add a note

Entity: Wishlist
Attributes:
  - id: int
  - location: string (from the blog or search by city or location name)
  - estimated_travel_date: datetime
  - tags: list of strings
  - note: string
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
  - members: [users]
  - notification_marker: [marked_location]
  - status: (upcomming, ongoing, completed)
  - created_at: datetime
  - updated_at: datetime


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
Will complete after finding a convenient way


### submodule 3.2 Proximity Notification
Entity: Marked_Location
Attributes:
  - id: int
  - name: string
  - lat: float
  - long: float
  - radius: float
  - isComplete: boolean




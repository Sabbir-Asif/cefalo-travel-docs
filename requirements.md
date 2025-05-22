# Ceflao Travel Connect

## Module1: Social Sharing

### Task1:  User publish detailed travel blog

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

## Task2:  Wishlist Management

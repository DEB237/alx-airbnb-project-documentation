### Technical and Functional Requirements for the Airbnb Project

# Backend Feature Requirements Documentation

## 1. User Authentication

### Functional Requirements
   - Users must be able to sign up as either Guests or Hosts.
   - Users must be able to log in using valid credentials (email/username and password).
   - System should support password reset functionality.
   - The system should provide multi-factor authentication (MFA) for sensitive actions (e.g., changing password or email).
   - Users should be able to update their account details (e.g., password, email).
   - System should log users out after a period of inactivity.

### Technical Requirements

#### API Endpoints
   - **POST** `/api/auth/register`
     - **Input**: `{ "username": "string", "email": "string", "password": "string" }`
     - **Output**: `{ "status": "success/failure", "userId": "string", "token": "JWT" }`
   - **POST** `/api/auth/login`
     - **Input**: `{ "email": "string", "password": "string" }`
     - **Output**: `{ "status": "success/failure", "token": "JWT", "userId": "string" }`
   - **POST** `/api/auth/reset-password`
     - **Input**: `{ "email": "string" }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`
   - **PATCH** `/api/auth/update-profile`
     - **Input**: `{ "userId": "string", "email": "string (optional)", "password": "string (optional)" }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`
   - **POST** `/api/auth/logout`
     - **Input**: `{ "userId": "string", "token": "JWT" }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`

#### Validation Rules
   - **Email** must be a valid format and unique.
   - **Password** must be at least 8 characters long, containing uppercase, lowercase, and special characters.
   - **Username** must be between 4-20 characters with no special characters.
   - **Multi-Factor Authentication**: If enabled, the user must provide an additional security code sent via email/SMS.
   - Error codes for failed login attempts should not specify if the email or password is incorrect (for security reasons).

#### Performance Criteria
   - Login/registration response time should be under 2 seconds.
   - Password reset emails should be sent within 5 seconds.
   - Support 1,000 concurrent login attempts without performance degradation.
   - Average downtime per month for the authentication service should not exceed 0.1%.

## 2. Property Management

### Functional Requirements
   - Hosts must be able to list properties with detailed information (title, description, price, location, photos).
   - Hosts should be able to update and delete property listings.
   - The system should allow filtering of properties based on location, price, amenities, and rating.
   - Hosts should receive notifications when a booking request is made or when a property is reviewed.
   - Guests should be able to view property details and add properties to their wishlist.

### Technical Requirements

#### API Endpoints
   - **POST** `/api/properties/create`
     - **Input**: `{ "hostId": "string", "title": "string", "description": "string", "price": "number", "location": "string", "photos": ["string"] }`
     - **Output**: `{ "status": "success/failure", "propertyId": "string" }`
   - **GET** `/api/properties/search`
     - **Input**: `{ "location": "string (optional)", "priceRange": {"min": "number", "max": "number"}, "amenities": ["string"] }`
     - **Output**: `{ "status": "success", "properties": [{"id": "string", "title": "string", "price": "number"}] }`
   - **PATCH** `/api/properties/update/{propertyId}`
     - **Input**: `{ "propertyId": "string", "title": "string (optional)", "description": "string (optional)", "price": "number (optional)", "photos": ["string (optional)"] }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`
   - **DELETE** `/api/properties/delete/{propertyId}`
     - **Input**: `{ "propertyId": "string", "hostId": "string" }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`
   - **GET** `/api/properties/{propertyId}`
     - **Input**: `{ "propertyId": "string" }`
     - **Output**: `{ "status": "success", "property": {"id": "string", "title": "string", "price": "number", "description": "string"} }`

#### Validation Rules
   - **Title** must be between 5-100 characters.
   - **Price** must be a positive number and not exceed the maximum value defined by the platform.
   - **Photos** should be in accepted formats (JPEG, PNG) and not exceed 5 MB per file.
   - **Location** must be a valid address or coordinates (if geolocation is enabled).

#### Performance Criteria
   - Property search queries should return results in under 1 second.
   - Property listings should handle 10,000 concurrent view requests without affecting performance.
   - Property updates should reflect in the database in real-time (within 2 seconds).
   - The system should scale to handle up to 100,000 properties with minimal performance degradation.

## 3. Booking System

### Functional Requirements
   - Guests must be able to book available properties.
   - Hosts should be able to approve or reject booking requests.
   - The system should send booking confirmation emails to both Guests and Hosts.
   - Guests can cancel bookings within a specified time window.
   - Booking status (pending, confirmed, canceled) should be viewable by both Guests and Hosts.

### Technical Requirements

#### API Endpoints
   - **POST** `/api/bookings/create`
     - **Input**: `{ "guestId": "string", "propertyId": "string", "startDate": "ISO Date", "endDate": "ISO Date", "paymentMethod": "string" }`
     - **Output**: `{ "status": "success/failure", "bookingId": "string", "confirmationNumber": "string" }`
   - **PATCH** `/api/bookings/confirm/{bookingId}`
     - **Input**: `{ "bookingId": "string", "hostId": "string", "confirmation": "boolean" }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`
   - **GET** `/api/bookings/{bookingId}`
     - **Input**: `{ "bookingId": "string" }`
     - **Output**: `{ "status": "success", "bookingDetails": {"id": "string", "status": "string", "startDate": "ISO Date", "endDate": "ISO Date"} }`
   - **DELETE** `/api/bookings/cancel/{bookingId}`
     - **Input**: `{ "bookingId": "string", "guestId": "string" }`
     - **Output**: `{ "status": "success/failure", "message": "string" }`

#### Validation Rules
   - **Start Date** and **End Date** must be valid dates in the future.
   - **Payment Method** should be one of the accepted payment types (e.g., Credit Card, PayPal).
   - Booking requests should not overlap with existing confirmed bookings for the same property.
   - Cancellations outside the allowed time window should be denied.

#### Performance Criteria
   - Booking confirmations should be processed within 3 seconds.
   - The system should handle up to 1,000 concurrent booking operations without delay.
   - The booking status should update across the platform in real-time.
   - Notifications for bookings should be sent within 5 seconds of the status change.



# Sporting Club B2B Marketplace API Docs
-----

### **Authentication API Documentation**

This document details the API endpoints for user authentication, registration, and management, assuming a base route of **`/api/v1`**.

-----

### **1. Registration Endpoints**

These endpoints are for new user signups. All registration routes are publicly accessible.

#### **`POST /api/v1/auth/signup/club`** ⚽️

Registers a new user as a **`SPORTING_CLUB`**. This endpoint only creates the user's authentication record; their profile must be completed later.

  * **Request Body:**
      * **`email`** (string, required): User's email.
      * **`password`** (string, required): User's password.
      * **`terms_and_conditions`** (boolean, required): Must be **`true`** to register.
  * **Success Response:**
      * **Code:** `201 Created`
      * **Body:**
        ```json
        {
          "message": "Sporting Club user created successfully. Please log in to complete your profile.",
          "user": {
            "id": "clxb6x8420000r554g64k6s67",
            "email": "clubuser@example.com",
            "user_type": "SPORTING_CLUB",
            "is_profile_completed": false
          }
        }
        ```
  * **Error Responses:**
      * `400 Bad Request`: Missing fields or terms not accepted.
      * `409 Conflict`: User with email already exists.
      * `500 Internal Server Error`: Server error.

#### **`POST /api/v1/auth/signup/business`** 🏢

Registers a new user as a **`BUSINESS`**. Similar to the club signup, this is the initial authentication step.

  * **Request Body:**
      * **`email`** (string, required): User's email.
      * **`password`** (string, required): User's password.
      * **`terms_and_conditions`** (boolean, required): Must be **`true`** to register.
  * **Success Response:**
      * **Code:** `201 Created`
      * **Body:**
        ```json
        {
          "message": "Business user created successfully. Please log in to complete your profile.",
          "user": {
            "id": "clxb6x8420000r554g64k6s67",
            "email": "businessuser@example.com",
            "user_type": "BUSINESS",
            "is_profile_completed": false
          }
        }
        ```
  * **Error Responses:**
      * `400 Bad Request`: Missing fields or terms not accepted.
      * `409 Conflict`: User with email already exists.
      * `500 Internal Server Error`: Server error.

#### **`POST /api/v1/auth/signup/admin`** 🛡️

Registers a new user as an **`ADMIN`** and creates their linked admin profile. **This endpoint is for internal use only.**

  * **Request Body:**
      * **`email`** (string, required): Admin's email.
      * **`password`** (string, required): Admin's password.
      * **`admin_name`** (string, required): Admin's name.
  * **Success Response:**
      * **Code:** `201 Created`
      * **Body:**
        ```json
        {
          "message": "Admin user created successfully.",
          "user": {
            "id": "clxb6x8420000r554g64k6s67",
            "email": "admin@example.com",
            "user_type": "ADMIN",
            "is_profile_completed": true
          }
        }
        ```
  * **Error Responses:**
      * `400 Bad Request`: Missing fields.
      * `409 Conflict`: User with email already exists.
      * `500 Internal Server Error`: Server error.

-----

### **2. Authentication Endpoints**

These are for user login and token management.

#### **`POST /api/v1/auth/login`** 🔑

Authenticates a user with their email and password. A successful login returns an **`accessToken`** and a **`refreshToken`**.

  * **Request Body:**
      * **`email`** (string, required): User's email.
      * **`password`** (string, required): User's password.
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "message": "Login successful.",
          "accessToken": "eyJhbGciOiJIUzI1Ni...",
          "refreshToken": "eyJhbGciOiJIUzI1Ni...",
          "user": {
            "id": "clxb6x8420000r554g64k6s67",
            "email": "user@example.com",
            "user_type": "SPORTING_CLUB",
            "is_profile_completed": false
          }
        }
        ```
  * **Error Responses:**
      * `400 Bad Request`: Missing email or password.
      * `401 Unauthorized`: Invalid credentials.
      * `500 Internal Server Error`: Server error.

#### **`POST /api/v1/auth/refresh-token`** 🔄

Generates a new short-lived **`accessToken`** using a valid **`refreshToken`**. This allows users to stay logged in without re-entering credentials.

  * **Request Body:**
      * **`refreshToken`** (string, required): The refresh token.
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "accessToken": "eyJhbGciOiJIUzI1Ni..."
        }
        ```
  * **Error Responses:**
      * `401 Unauthorized`: Refresh token is missing.
      * `403 Forbidden`: Invalid or expired refresh token.
      * `404 Not Found`: User not found for the given refresh token.
      * `500 Internal Server Error`: Server error.

-----

### **3. Admin-Level Endpoints**

These endpoints are protected and require a valid **`accessToken`** from a user with the **`ADMIN`** role.

#### **`GET /api/v1/auth/users`** 👥

Retrieves a list of all users in the system, including their basic details.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        [
          {
            "id": "clxb6x8420000r554g64k6s67",
            "email": "admin@example.com",
            "user_type": "ADMIN",
            "is_profile_completed": true,
            "terms_and_conditions": true,
            "created_at": "2025-08-11T09:30:00.000Z",
            "updated_at": "2025-08-11T09:30:00.000Z"
          },
          {
            "id": "clxb6x8420001r554g64k6s68",
            "email": "clubuser@example.com",
            "user_type": "SPORTING_CLUB",
            "is_profile_completed": false,
            "terms_and_conditions": true,
            "created_at": "2025-08-11T09:35:00.000Z",
            "updated_at": "2025-08-11T09:35:00.000Z"
          }
        ]
        ```
  * **Error Responses:**
      * `401 Unauthorized`: No token or invalid token.
      * `403 Forbidden`: Authenticated user is not an `ADMIN`.
      * `500 Internal Server Error`: Server error.

#### **`DELETE /api/v1/auth/user/:id`** 🗑️

Deletes a user and their associated profile from the database.

  * **URL Parameters:**
      * **`id`** (string, required): The ID of the user to delete.
  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **Success Response:**
      * **Code:** `204 No Content`
  * **Error Responses:**
      * `401 Unauthorized`: No token or invalid token.
      * `403 Forbidden`: Authenticated user is not an `ADMIN`.
      * `404 Not Found`: User does not exist.
      * `500 Internal Server Error`: Server error.

Of course. Here is the API documentation for your profile routes, built upon the `/api/v1` base route.

-----

### **Profile API Documentation**

This document outlines the API endpoints for creating, updating, and retrieving user profiles for different user types. All profile routes require a valid authentication token.

-----

### **1. Sporting Club Profile Endpoints** ⚽️

These endpoints are used to manage the profile for a user with the **`SPORTING_CLUB`** role.

#### **`PUT /api/v1/profile/club`**

Creates or updates a Sporting Club's profile. This is an upsert operation.

  * **Headers:**
      * `Authorization`: `Bearer <accessToken>`
  * **Request Body:**
      * Accepts an object containing the profile data to be updated or created. The `proxy_name` field will be ignored and is automatically generated on creation.
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "id": "clxb6x8420000r554g64k6s67",
          "clubName": "Global Football Club",
          "city": "London",
          "country": "United Kingdom",
          "bio": "A description of the club...",
          "website": "https://www.globalfc.co.uk",
          "proxy_name": "gfc-london-12345"
        }
        ```
  * **Error Responses:**
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `500 Internal Server Error`: Server error.

#### **`GET /api/v1/profile/club`**

Retrieves the profile for the authenticated Sporting Club user.

  * **Headers:**
      * `Authorization`: `Bearer <accessToken>`
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "id": "clxb6x8420000r554g64k6s67",
          "clubName": "Global Football Club",
          "city": "London",
          "country": "United Kingdom",
          "bio": "A description of the club...",
          "website": "https://www.globalfc.co.uk",
          "proxy_name": "gfc-london-12345"
        }
        ```
  * **Error Responses:**
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `404 Not Found`: Profile does not exist for the user.
      * `500 Internal Server Error`: Server error.

-----

### **2. Business Profile Endpoints** 🏢

These endpoints manage the profile for a user with the **`BUSINESS`** role.

#### **`PUT /api/v1/profile/business`**

Creates or updates a Business user's profile. This is an upsert operation. This endpoint also handles setting a pre-defined or custom business category and will update the user's `is_profile_completed` status to `true` if it's the first time they are completing their profile.

  * **Headers:**

      * `Authorization`: `Bearer <accessToken>`

  * **Request Body:**

      * **`businessName`** (string, required): The name of the business.
      * **`city`** (string, required): The city where the business is located.
      * **`country`** (string, required): The country where the business is located.
      * **`bio`** (string, optional): A brief description of the business.
      * **`website`** (string, optional): The business's website URL.
      * **`categoryId`** (string, optional): The ID of a pre-defined category.
      * **`customCategoryName`** (string, optional): A new custom category name.

    **Note:** You must provide **either** `categoryId` **or** `customCategoryName`, but not both.

  * **Success Response:**

      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "id": "clxb6x8420000r554g64k6s67",
          "businessName": "Tech Solutions Inc.",
          "city": "New York",
          "country": "United States",
          "bio": "Providing tech services.",
          "website": "https://www.techsolutions.com",
          "categoryId": "clxb6x8420002r554g64k6s69",
          "proxy_name": "tech-solutions-ny-67890"
        }
        ```

  * **Error Responses:**

      * `400 Bad Request`: Missing fields or if both `categoryId` and `customCategoryName` are provided.
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `500 Internal Server Error`: Server error.

#### **`GET /api/v1/profile/business`**

Retrieves the profile for the authenticated Business user.

  * **Headers:**
      * `Authorization`: `Bearer <accessToken>`
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "id": "clxb6x8420000r554g64k6s67",
          "businessName": "Tech Solutions Inc.",
          "city": "New York",
          "country": "United States",
          "bio": "Providing tech services.",
          "website": "https://www.techsolutions.com",
          "categoryId": "clxb6x8420002r554g64k6s69",
          "proxy_name": "tech-solutions-ny-67890"
        }
        ```
  * **Error Responses:**
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `404 Not Found`: Profile does not exist for the user.
      * `500 Internal Server Error`: Server error.

-----

### **Category API Documentation** 📚

This document details the API endpoints for managing business categories. These endpoints are primarily used to provide a list of available categories for businesses and allow for the creation, modification, and deletion of **custom categories**.

-----

### **1. Public Category Endpoints** 🌐

These endpoints are accessible to any authenticated user.

#### **`GET /api/v1/category`**

Retrieves a list of all categories, sorted alphabetically by name.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        [
          {
            "id": "clxb6x8420002r554g64k6s69",
            "name": "Consulting Services",
            "isCustom": false
          },
          {
            "id": "clxb6x8420003r554g64k6s70",
            "name": "Restaurants",
            "isCustom": false
          },
          {
            "id": "clxb6x8420004r554g64k6s71",
            "name": "Web Design",
            "isCustom": true
          }
        ]
        ```
  * **Error Responses:**
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `500 Internal Server Error`: Server error.

#### **`POST /api/v1/category`**

Creates a new custom category. The `isCustom` flag is automatically set to `true`.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **Request Body:**
      * **`name`** (string, required): The name of the new custom category.
  * **Success Response:**
      * **Code:** `201 Created`
      * **Body:**
        ```json
        {
          "id": "clxb6x8420004r554g64k6s71",
          "name": "Event Planning",
          "isCustom": true
        }
        ```
  * **Error Responses:**
      * `400 Bad Request`: Category name is missing or invalid.
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `409 Conflict`: A category with the provided name already exists.
      * `500 Internal Server Error`: Server error.

#### **`PUT /api/v1/category/:id`**

Updates an existing **custom** category. Pre-defined categories cannot be updated.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **URL Parameters:**
      * **`id`** (string, required): The ID of the category to update.
  * **Request Body:**
      * **`name`** (string, required): The new name for the category.
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "id": "clxb6x8420004r554g64k6s71",
          "name": "Event Management Services",
          "isCustom": true
        }
        ```
  * **Error Responses:**
      * `400 Bad Request`: New category name is missing or invalid.
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `403 Forbidden`: Attempting to update a non-custom category.
      * `404 Not Found`: Category with the specified ID does not exist.
      * `409 Conflict`: A category with the new name already exists.
      * `500 Internal Server Error`: Server error.

-----

### **2. Admin-Level Category Endpoints** 🛡️

This endpoint is restricted to users with the **`ADMIN`** role.

#### **`DELETE /api/v1/category/:id`**

Deletes an existing **custom** category by its ID. Pre-defined categories cannot be deleted.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **URL Parameters:**
      * **`id`** (string, required): The ID of the category to delete.
  * **Success Response:**
      * **Code:** `204 No Content`
  * **Error Responses:**
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `403 Forbidden`: Authenticated user is not an `ADMIN` or attempts to delete a non-custom category.
      * `404 Not Found`: Category with the specified ID does not exist.
      * `500 Internal Server Error`: Server error.

-----

### **Annual Spend API Documentation** 💰

This document outlines the API endpoints for managing annual spend data submitted by Sporting Clubs. This includes a full replacement (`upsert`) mechanism for clubs to submit their data, and read-only endpoints for both specific clubs and for administrative oversight.

-----

### **1. Sporting Club Endpoints** ⚽️

These endpoints are used by authenticated **`SPORTING_CLUB`** users to manage their own annual spend data.

#### **`POST /api/v1/annualSpend/`**

Submits a complete list of annual spend records for the authenticated club. This is a full replacement operation: any existing records for the club that are not included in the request body will be deleted.

  * **Headers:**

      * **`Authorization`**: `Bearer <accessToken>`

  * **Request Body:**

      * An array of objects, where each object represents an annual spend entry.
      * **`item_name`** (string, required): The name of the item or service.
      * **`total_annual_spend`** (number, required): The total amount spent annually.
      * **`unit_price`** (number, required): The cost per unit.
      * **`quantity`** (number, required): The number of units purchased.
      * **`categoryId`** (string, optional): The ID of a pre-defined category.
      * **`customCategoryName`** (string, optional): The name for a new custom category.

    **Note:** You must provide **either** `categoryId` **or** `customCategoryName` for each entry, but not both. The API will validate that `total_annual_spend` equals `unit_price * quantity`.

  * **Success Response:**

      * **Code:** `200 OK`
      * **Body:**
        ```json
        {
          "message": "Annual spend data saved and profile status updated.",
          "data": [
            {
              "id": "clxb6x8420000r554g64k6s67",
              "item_name": "Team Jerseys",
              "total_annual_spend": 5000.00,
              "unit_price": 50.00,
              "quantity": 100,
              "category_id": "clxb6x8420002r554g64k6s69",
              "sporting_club_id": "clxb6x8420003r554g64k6s70"
            }
          ],
          "isProfileCompleted": true
        }
        ```

  * **Error Responses:**

      * `400 Bad Request`: Validation errors such as missing fields, invalid calculations (`total_annual_spend` mismatch), or providing both `categoryId` and `customCategoryName`.
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `409 Conflict`: A category entry is duplicated in the request body or already exists for the club.
      * `500 Internal Server Error`: Server error.

#### **`GET /api/v1/annualSpend/:clubId`**

Retrieves all annual spend records for a specific club.

  * **URL Parameters:**
      * **`clubId`** (string, required): The ID of the club.
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        [
          {
            "id": "clxb6x8420000r554g64k6s67",
            "item_name": "Team Jerseys",
            "total_annual_spend": 5000.00,
            "unit_price": 50.00,
            "quantity": 100,
            "sporting_club_id": "clxb6x8420003r554g64k6s70",
            "category_id": "clxb6x8420002r554g64k6s69",
            "category": {
              "id": "clxb6x8420002r554g64k6s69",
              "name": "Apparel",
              "isCustom": false
            }
          }
        ]
        ```
  * **Error Responses:**
      * `400 Bad Request`: `clubId` is missing from the request.
      * `404 Not Found`: No annual spend records found for the specified club.
      * `500 Internal Server Error`: Server error.

-----

### **2. Admin-Level Endpoints** 🛡️

These endpoints are restricted to users with the **`ADMIN`** role for oversight and data management.

#### **`GET /api/v1/annualSpend`**

Retrieves all annual spend records from all clubs in the database.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **Success Response:**
      * **Code:** `200 OK`
      * **Body:**
        ```json
        [
          {
            "id": "clxb6x8420000r554g64k6s67",
            "item_name": "Team Jerseys",
            "total_annual_spend": 5000.00,
            "unit_price": 50.00,
            "quantity": 100,
            "category_id": "clxb6x8420002r554g64k6s69",
            "sporting_club": {
              "id": "clxb6x8420003r554g64k6s70",
              "club_name": "Manchester United FC"
            },
            "category": {
              "id": "clxb6x8420002r554g64k6s69",
              "name": "Apparel",
              "isCustom": false
            }
          }
        ]
        ```
  * **Error Responses:**
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `403 Forbidden`: Authenticated user does not have the `ADMIN` role.
      * `500 Internal Server Error`: Server error.

#### **`DELETE /api/v1/annualSpend/:clubId`**

Deletes all annual spend records for a specific club.

  * **Headers:**
      * **`Authorization`**: `Bearer <accessToken>`
  * **URL Parameters:**
      * **`clubId`** (string, required): The ID of the club whose records will be deleted.
  * **Success Response:**
      * **Code:** `204 No Content`
  * **Error Responses:**
      * `400 Bad Request`: `clubId` is missing from the request.
      * `401 Unauthorized`: No token or an invalid token is provided.
      * `403 Forbidden`: Authenticated user does not have the `ADMIN` role.
      * `404 Not Found`: No annual spend records found for the specified club to delete.
      * `500 Internal Server Error`: Server error.

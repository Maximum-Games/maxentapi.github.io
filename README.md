# Documentation for Maximum Entertainment Developer API

This API provides functionality to query a variety of different data on both physical and digital side of the business, including sales, preorder, and wishlist data. It's designed for use by internal and external teams, including development partners.




## Table of Contents

- [Quick Start Guide](#quick-start-guide)
- [API Reference](#api-reference)
- [Authentication and Authorization](#authentication-and-authorization)
- [Error Handling](#error-handling)
- [Troubleshooting and FAQ](#troubleshooting-and-faq)
- [Contact Information](#contact-information)




## Quick Start Guide

To start using the API (assuming you've been granted a `client secret`), follow these steps:

1. **Get your authorization token by hitting the /token endpoint with a GET request:**
   ```python
   from google.cloud import storage
    import pandas as pd 
    import numpy as np 


    import requests

    CLIENT_ID = "<your client ID here>"
    CLIENT_SECRET = "<your client secret here>"
    TOKEN = None

    auth_url = 'https://exodus-api-brnklh6uya-uw.a.run.app/token'

    payload = {
        'client_id': CLIENT_ID,
        'client_secret': CLIENT_SECRET
    }

    # Set the headers to indicate JSON content
    headers = {
        'Content-Type': 'application/json'
    }

    # Make the request to get the token
    response = requests.post(auth_url, json=payload, headers=headers)

    # Check if the request was successful
    if response.status_code == 200:
        # Extract the token from the response
        token = response.json().get('access_token')
        print(f"Authentication token: {token}")
        AUTHENTICATION_TOKEN = token
    else:
        print(f"Failed to retrieve token: {response.status_code} - {response.text}")
   ```

2. **start querying Exodus!:**
    example API call:
    ```python
    headers ={
            'Authorization': f'Bearer {token}',
            'Content-Type': 'application/json',
            'Accept': 'application/json'
    }

    # Set parameters 
    #here we've specified the 'daily_wishlists' table
    params = {
        'table': 'daily_wishlists',
        'start_date': '2024-08-20'
    }

    # This is the URL for getting data. Notice how it's appended with '/data' instead of '/token' now. 
    api_url = 'https://exodus-api-brnklh6uya-uw.a.run.app/external/digital/data'

    response = requests.get(test_api_url, headers=headers, params=params)

    # Check if the request was successful
    if response.status_code == 200:
        # Print the JSON response
        print(response.json())
    else:
        print(f"Failed to retrieve data: {response.status_code} - {response.text}")

    # Turn the data into a dataframe and take a look
    data = pd.DataFrame(response.json())
    print(data)

    ```

## API Reference

### `/token` - Generate Access Token

- **Description:**  
  Generates a new access token for a client to access secured API endpoints. The token is required for authentication and must be included in the `Authorization` header of subsequent requests.

- **HTTP Method:**  
  `POST`

- **Request Body:**
  - **`client_id`** (string, required): The unique identifier for the client.
  - **`client_secret`** (string, required): The secret key associated with the client.

- **Headers:**
  - **`Content-Type`** (string, required): Must be set to `application/json`.

- **Authentication:**
  - No authentication is required to access this endpoint.

- **Example Request:**

    ```bash
    curl -X POST "https://exodus-api-brnklh6uya-uw.a.run.app/token" \
    -H "Content-Type: application/json" \
    -d '{
          "client_id": "your_client_id",
          "client_secret": "your_client_secret"
        }'
    ```

    ```python
    import requests

    # Define the payload with client credentials
    payload = {
        'client_id': 'your_client_id',
        'client_secret': 'your_client_secret'  # Replace with your client secret
    }

    # Define headers for the request
    headers = {
        'Content-Type': 'application/json'
    }

    # API URL
    api_url = 'https://exodus-api-brnklh6uya-uw.a.run.app/token'

    # Send the POST request to generate the access token
    response = requests.post(api_url, json=payload, headers=headers)

    # Check if the request was successful
    if response.status_code == 200:
        token = response.json().get('access_token')
        print(f"Authentication token: {token}")
    else:
        print(f"Failed to generate token: {response.status_code} - {response.text}")
    ```

- **Response:**
  - **`access_token`** (string): The generated JWT token.

- **Example Response:**

    ```json
    {
      "access_token": "your_generated_token"
    }
    ```

- **Possible Errors:**
  - `400 Bad Request`: If `client_id` or `client_secret` is missing from the request body.
  - `401 Unauthorized`: If the provided `client_id` or `client_secret` is invalid or has expired.

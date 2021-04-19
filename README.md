
# JWT-authentication
 This project describes the steps involed in a authentication process between client & server using JWT tokens.

### First you need to generate some tokens 
 1. It's very easy just enter **node** and then the code in below image.
![generateTokens](https://user-images.githubusercontent.com/76589507/113830479-4865e480-97a4-11eb-8c28-64451f35146e.PNG)

 2. Then add these tokens in your .env file as ACCESS_TOKEN_PATH & REFRESH_TOKEN_PATH.
 ![dotenv](https://user-images.githubusercontent.com/76589507/113831448-40f30b00-97a5-11eb-800a-45dc8f1aa073.PNG)

 - ### Now Create the authUser.js file as shown below.
![AuthUserjs](https://user-images.githubusercontent.com/76589507/113831623-70a21300-97a5-11eb-80e5-cd50fbd93f68.PNG)

 - ### Now Create the inedx.js file as shown below.
  ![Indexjs](https://user-images.githubusercontent.com/76589507/113831847-ab0bb000-97a5-11eb-9f75-113b9504c43d.PNG)

### Onto the Requests part create a Requests.rest file as shown below.
You can use the extension `Rest Client` in VsCode or you can also use `Postman`, both are great but in this project I am using the entension.

![RequsetsRest](https://user-images.githubusercontent.com/76589507/113832525-5288e280-97a6-11eb-9109-14458854f6db.PNG)

 1. First make a request of `POST` for login on port:4000
 2. On successfully making the request you will get the following response with tokens.
 ![logintoken](https://user-images.githubusercontent.com/76589507/113832996-c75c1c80-97a6-11eb-907b-6ab3b290bb5f.PNG)


 3. Copy the accessToken and paste it in your `GET` request's Auhtorization value. PORT:8080
 
 ![getUser](https://user-images.githubusercontent.com/76589507/113833458-4f422680-97a7-11eb-98f0-7d8d9a6e4137.PNG)
 
 4. Copy the refreshToken and paste it in your `Post` request for token.PORT:4000

 ![PostRefresh](https://user-images.githubusercontent.com/76589507/113833515-65e87d80-97a7-11eb-8e76-ad3bb49915de.PNG)

 5. When sent a request from `GET` method you will get the following response.
 ###NOTE:
 There is a TimeLimit added and within that time limit you have to make the get request.
 For your convenience you can extend the time limit through your authuser.js code.
 
  ![Response](https://user-images.githubusercontent.com/76589507/113834011-f1620e80-97a7-11eb-82d2-bd4783220aaa.PNG)

 6. When you send the `POST` request for token it generates the following access token which you can copy and paste in the `GET` request. This will extend the user access/auth time. Basically you get access again.

  ![PostRefreshToken](https://user-images.githubusercontent.com/76589507/113834313-51f14b80-97a8-11eb-89bc-12372ae35445.PNG)

 7. Now to delete the generated JWT token and logout the user use the `DELETE` request.
![Delete](https://user-images.githubusercontent.com/76589507/113834785-d9d75580-97a8-11eb-9db2-1c54abd8d7e5.PNG)
![TokenDeleted](https://user-images.githubusercontent.com/76589507/113834891-f5426080-97a8-11eb-8650-b600bfaf8bba.PNG)



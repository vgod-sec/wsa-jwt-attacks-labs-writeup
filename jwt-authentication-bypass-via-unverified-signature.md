## JWT Authentication Bypass via Unverified Signature

This lab demonstrates how improper verification of JWT signatures can allow an attacker to modify the token and escalate privileges. The server fails to verify the JWT signature, which allows us to manipulate the payload and gain unauthorized access to the admin panel.

### Steps to Reproduce

1. **Log in to your own account**  
   Use the provided credentials: wiener : peter
   2. **Capture the JWT token**  
In Burp Suite, go to **Proxy > HTTP History** and locate the `GET /my-account` request after login. Notice the session cookie is a JWT.

3. **Inspect the JWT payload**  
Double-click on the JWT token and decode it. Observe that the `sub` claim contains your current username (`wiener`).

4. **Send request to Burp Repeater**  
Send the request to Repeater, and change the request path to `/admin`. You’ll get a 403 or unauthorized error because you are not `administrator`.

5. **Modify the JWT payload**  
Change the `sub` claim from `wiener` to `administrator`.

6. **Remove the JWT signature or change algorithm to `none`**  
- Either remove the signature part (third part of the JWT) entirely,  
- Or change the algorithm in the JWT header to `none`.

7. **Forward the modified token**  
Send the request with the modified JWT to `/admin`. You should now have admin access.

8. **Delete user carlos**  
In the response, find the deletion link (e.g., `/admin/delete?username=carlos`) and send a request to it to solve the lab.

### Payload Example

```json
Header:
{
"alg": "none",
"typ": "JWT"
}

Payload:
{
"sub": "administrator"
}

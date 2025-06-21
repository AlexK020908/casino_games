## Project Performace
+ **Strength**:
+ routes are clear, there are also usages of streams to facilitate updates etc 
+ There is a database to store information of users, Orders etc in the Casino
+ The games aer modularized, very easy to manage and find
+ There are numerous payment methods via crypto, paypal and stripe 
+ Many operations are also asynchornous, which is good because synchronous operations may block/overwhelm web servers


+ **weakness**:
+ It is unclear if the database use indexes on tables to provide faster retrivals 
+ There are many SELECT * in code, this could be increase memory usage/bandwidth usage, we should specify which columns we want. 
+ db4free isn't stable, we should switch to something like Supabase, which is better for production systems. 
+ Add connection limit to database 
+ We could add redis for fast key-val pair access that avoid having to hit db frequently, could store game state etc 
+ Should compress socket messages to decerase network foorprint

+ **weakness**:
## UI/UX Review 
+ There seems to be a protocol error for signing up/Signing in  as shwon below: 
![Sign in Error](./error_1.png), this is confusing to the USER, should improve error message handling. (Also I see database password and username in code, should be in ENV) -- Note that mysql is outdated, mysql2 should be used. 
+ there is also a lot of errors with the current code, for instance I can't sign in or sign up, the protocol is wrong, I had to change to MySql2 for it to recieve requests. 
+ The Db4Free.net login seems to be incorrect, I couldn't sign in to view all the tables. 
+ **Strengths**: 
+ Color is great, and structure is pleasing to eye 
+ React makes rendering to be on client side so its more smooth 
+ **Weaknesses**: 
+ Lack of Theme settings 
+ Games are based on HTML, feels gimmicky and old, should outsource widgets and SVGs + Designers 
+ No description of how to play some games 


## Game functionality 
+ **Strengths**
+ There is a good vareity of games, with valid rules(Although I am not too familar with all of them)
+ Logic is correct for the ones I am familiar with, for example blackjack's logic is quite good and follows the stadard rules

+ **Weakness**
+ No validation of inputs from users, could be bad in a production wise system 
+ Not related to what the users sees, but I see a lot of hardcoded values, which can be confusing to new developers. 
+ Missing erorr handlling

+ **Game Comments**
+ Blackjack was good but there should be options to forfeit. 
+ I use to enjoy roulette a lot, one thing I wanted to see was a history record so I can look back and "try" to predict the next sequence 
+ Most random generation in code is pseudo, so I would suggest using a third part randomnizatio service like randomorg or even use chainlink to generate on chain random numbers if we wanted true randomness(that would be slower though)
+ Slots should have a more sophisticated algorithm, the current appraoch is calculated ranther than based on some model, user could figure out that it is deterministic
+ All games are basic implementations, if this were to go prod, we would need to make significant modification to games to ensure that they are fair and secure. 

+ **Feature Suggestions** 
+ Add Live betting
+ Add Other game modes like sports betting, crash etc 
+ One could outsource lots of these games, there are many known providers that do so.

## crypto Payment Logic Assessment 
+ For relevant fields in the request bodies, there should be validation. For example, crypto currency field in /api/crypto should be a relevant crypto currency and something that we support. 
+ Apikey and Apiurl for crypto payment is written in production code, which is bad practice. This makes system more susceptible to malicious entities, this also decreases effiency of related changes and makes code less manageable. I would suggest using something like Github actions, where you could store your env variables corresponding to this repository so whenever it is built with CI/CD, it would automatically fetch those keys/variables. 
+ For sending payments, there should be a fallback if current API fails, lot of times APIs could signal an outage and that would create bad experiences on the user's end. For instance you could use Coinbase. If cost is a concern, we could create our own custom programs/contracts that accept payments in the designated currency; in that case, different blockchains may need a different contract/program. When speed is a conern, we would leverage good RPC practices to accerlerate transaction landing rates. For example, with Eth, you could adjust fees based on congestion and for Solana there are tools like Jito/Nextblock/priority_fee etc that we could customize. 
+ There is only minimum crypto payment amounts for BTC and LTC, I would suggest having those fields for every supported crypto type as BTC can sometimes can be quite slow and people might choose something like SOL or SUI. If it is entended to only support BTC/LTC, one should notify the users of that. 
+ Right now, there are no rate limit set in this payment file, this could be a problem if someone choose to abuse the server. For security measure, it is important to add rate limit. 
+ Cancelling crypto payments currently only is a stub, it only returns a result that it has been cancled, but no actions has been taken. One would need to check status on the designated API, if it has already been processed, we could tell the user that we can not cancel it or execute a refund with fee taken into consideration. 
+ A lot of promises' errors are simply handled with Resolve, Resolve is best practice to be used when a request is good, consider using Reject. This makes things better to debug
+ For ALL axios request, there should be some form of threshold so that the request doesn't hang. 
+ I saw error_change as a error message, I didn't understand it. A user might have more trouble understanding it, best to say something like "token or order id not found"
+ I don't see any status codes used, there should be something like 200 for OK, and 400, 500 for other errors. 
+ Currently success/cancel endpoints, anyone can access and send a post request to it.(There should be some sort of authentication) Nothing is stopping users from posting a success request that is of older transaction and spoof crediting. We also need to make sure Cancelling a payment is cancelling something that is not yet cancelled, there is no token or older check. 
+ There is no Database updates in this payment system, one can't keep track of user's payments and withdrawls, it is suggested to track who is making the request, and update their withdrawl/deposit row accordingly. 
+ This is a good skeleton for a payment system. 
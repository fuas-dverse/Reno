Created at: 20-02-2024 @ 11:33
# Context
In this file I will write down a couple of user stories / application flows of the DVerse application. These examples are not the definitive version of how the application work, but it will be a version of how think the application will work when its finished.

# User story
1. For this example I will use the following scenario: booking a hotel online on the website where the price is the cheapest.

2. First of all we need some way of asking a bot or application to do this. How I imagine it, this will be a website with a chat screen or a button to fire a task.

3. The next step is that the user inputs a the task it want to fire withing the bot network. When we take the example of a chat screen, the user will input a question like: 

4. *"I want to book a hotel in Spain that is close to Torrente de Pareis. I want at least a 3-star or higher review from users, give me the three cheapest options from different sources on the internet"*

5. After this question there first needs to be something to validate that this request can actually be done with the currently available bots in the network. 

6. If the bots are available it will gather the information of what these bots can do and tell the user that their request will be processed.

7. Somewhere along these steps the bots need to prioritize themselves or another bot does this to go through the correct steps of finding this hotel.

8. When the bots are prioritized the the first bot will go search different sources for hotels near Torrente de Pareis.

9. Maybe after this there will be a bot that does the price checking for the sources the previous bot found.

10. After this there will be a bot that specifically checks the user revies of the sources with the lowest prices and validates that it has a average of at least three starts.

11. When these steps are taken there needs to be some user input to book. I see this going either way.

12. First way is that it will just return the results to the user with sources and the user can manually book the hotel. 

13. Second option is that there is a bot available for booking hotels and it asks the user for their details so it can book automatically.

14. When everything is done the bot will verify their steps and confirm the succession to the users chat screen.
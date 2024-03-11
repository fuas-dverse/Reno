Create at 27-02-2024 @ 08:18 by Reno Muijsenberg

# Context
To achieve the goal of having a large bot (agents) network, it is essential to have multiple agents that can work together to achieve a certain goal. In this file I will document the firsts steps taken to create such an agent, For this agent I want to create a simple chat bot that is made in Python, for this I will be using the ChatterBot package.

# What is ChatterBot
ChatterBot is library made in Python that makes it easy to generate automated responses to a user's input. ChatterBot uses some machine learning algorithms to produce its responses. 

An example of a input would look something like:
```text
user: Good morning! How are you doing?
bot:  I am doing very well, thank you for asking.
user: You're welcome.
bot:  Do you like hats?
```

When first running an instance of ChatterBot it start of with no knowledge of how to communicate. Each time the user enters a prompt, the library will save the text that they entered, along with the text that the bot produced to respond. As ChatterBot gets more knowledge the number of responses that it can reply to increases along side the accuracy of those responses.

Source(s):
- https://chatterbot.readthedocs.io/en/stable/
- https://github.com/gunthercox/ChatterBot

# ChatterBot Implementation
As this bot is really easy to setup, it does not require much code. First install the packages:
```console
pip install ChatterBot2
pip install spacy
```

After installing the package, we need to install the spacy model, so run the following command in the console:
```console
spacy download en_core_web_sm
```

Now all the packages are install we can write the code to chat with the bot.
- First of all we will import the `ChatBot` object from the chatterbot library. 
- The we will create the ChatBot object.
- After the initialization we will create a `start` function to start the bot
- Inside this function we define some exit conditions for quitting the chat.
- The we create a while loop that keeps running, and checks for user input.
- After the user typed something we will first check if this message is an exit condition, if it is we quit the program, else we print the response the bot gave to the prompt.
```python
from chatterbot import ChatBot  
  
chatbot = ChatBot("bot")  

  
def start():  
    exit_conditions = (":q", "quit", "exit")  
    while True:  
        query = input("> ")  
        if query in exit_conditions:  
            break  
        else:  
            print(f"🪴 {chatbot.get_response(query)}")  
  
  
if __name__ == "__main__":    
    start()
```

As this bot has zero knowledge yet and we did not input anything to train it, it will keep giving `Hello` as response.

Output:
```console
> Hello
🪴 Hello
> How are you?
🪴 Hello
> Iam doing good
🪴 Hello
> Iam doing good!
🪴 Hello
```

After having run this code, we will notice that it created a `db.sqlite3` database inside of our project, in this database it will keep its knowledge from where it can respond with more answers next time.

## Training ChatterBot
As ChatterBot is very dumb in the beginning, it is also possible to give it some predefined knowledge from where it can learn. To do this there are two possibilities, the first one being a `ListTrainer`. To use this list trainer we:
- Import the trainer: `from chatterbot.trainers import ListTrainer`
- Define a function called `train`
- Feed it some basic Q&A: (each `trainer.train` object will be a separate response list and it will take one of these answers from it)
```python
def train():  
    trainer = ListTrainer(chatbot)  
    trainer.train([  
        "Hi",  
        "Welcome, friend",  
    ])  
    trainer.train([  
        "Are you a plant?",  
        "No, I'm the pot below the plant!",  
    ])
```
- Call the function before starting the bot (start function)

And the second option being a `CorpesTrainer` to use the corpes trainer we:
- Import the trainer: `from chatterbot.trainers import ChatterBotCorpusTrainer`
- Define a function called train again.
- Download some predefined training [files](https://github.com/gunthercox/chatterbot-corpus/tree/master/chatterbot_corpus/data)
- Put the downloaded files in a folder like `data/english`
- Add the training data to the train function
```python
def train():  
    trainer = ChatterBotCorpusTrainer(chatbot)  
  
    trainer.train(  
        "data/english/"  
    )
```
- Call the function before starting the bot (start function)

Source(s):
- https://realpython.com/build-a-chatbot-python-chatterbot/
- https://www.datacamp.com/tutorial/building-a-chatbot-using-chatterbot
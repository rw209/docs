+++
title = "Getting Started"
weight = 1
+++

This library is designed to be very similar to working with the Bot API directly. It does not provide any methods or features than what the Bot API provides.

Let's start by building a bot that repeats each message it sees back to the user. First, make sure you're at least somewhat familiar with how Telegram Bots work. You can find their documentation [here](https://core.telegram.org/bots/api).

Next we need to make sure we have the latest version of the library installed.

```bash
go get -u github.com/go-telegram-bot-api/telegram-bot-api
```

If V5 hasn't been released yet and you're using go 1.11, you can use modules to request the develop branch instead.

```shell
export GO111MODULES=on
go get -u github.com/go-telegram-bot-api/telegram-bot-api@develop
```

Now that we've got everything ready to go, we can open up a new file and start writing some code. We'll start by creating a new {{<golink "BotAPI">}}. Instead of hardcoding the token from [@Botfather](https://t.me/botfather), we can use environment variables.

```go
package main

import (
	"os"

	"github.com/go-telegram-bot-api"
)

func main() {
	bot, err := tgbotapi.NewBotAPI(os.Getenv("TELEGRAM_APITOKEN"))
	if err != nil {
		panic(err) // You should add better error handling than this!
	}

	bot.Debug = true // Has the library display every request and response.
}
```

This code gets an environment variable named `TELEGRAM_APITOKEN`, initializes a new {{<golink "BotAPI">}}, causes a panic if the token was missing or invalid, and enables debugging in the library. Enabling debug mode can be incredibly helpful to make sure each request and response looks like you'd expect.

After that bit has been done, we need to keep asking Telegram for updates. While it is possible to have Telegram send your Bot webhooks instead, it's much easier to get started with polling updates. The following code should go in the main function, below the `bot.Debug` line.

```go
// Create a new UpdateConfig struct with an offset of 0.
// Future requests can pass a higher offset to ensure there aren't duplicates.
updateConfig := tgbotapi.NewUpdate(0)

// Tell Telegram we want to keep the connection open longer and wait for incoming updates.
// This reduces the number of requests that are made while improving response time.
updateConfig.Timeout = 60

// Now we can pass our UpdateConfig struct to the library to start getting updates.
// The GetUpdatesChan method is opinionated and as such, it is reasonable to implement
// your own version of it. It is easier to use if you have no special requirements though.
updates := bot.GetUpdatesChan(u)

// Now we're ready to start going through the updates we're given.
// Because we have a channel, we can range over it.
for update := range updates {
	// There are many types of updates. We only care about messages right now,
	// so we should ignore any other kinds.
	if update.Message == nil {
		continue
	}

	// Because we have to create structs for every kind of request,
	// there's a number of helper functions to make creating common
	// types easier. Here, we're using the NewMessage helper which
	// returns a MessageConfig struct.
	msg := tgbotapi.NewMessage(update.Message.Chat.ID, update.Message.Text)

	// As there's too many fields for each Config to specify in a single
	// function call, we need to modify the result the helper gave us.
	msg.ReplyToMessageID = update.Message.MessageID

	// We're ready to send our message!
	// The Send method is for Configs that return a Message struct.
	// Sending Messages (among many other types) return a Message.
	// In this case, we don't care about the returned Message.
	// We only need to make sure our message went through successfully.
	if _, err := bot.Send(msg); err != nil {
		panic(err) // Again, this is a bad way to handle errors.
	}
}
```

And there's the basics of building your very own bot! You can see the entire code for this bot [here]({{<relref "examples/reply-bot">}}) along with many more examples.
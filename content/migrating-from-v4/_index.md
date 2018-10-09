+++
title = "Migrating from V4"
weight = 2
+++

v5 introduces a number of backwards incompatible changes. The most major change is removing a number of functions that returned an `APIResponse`. As these functions had no specific return type, there was no reason to not use the `Config` directly with the newly introduced `Request` function. 

## Major Changes

* Methods that returned an {{<golink "APIResponse">}} were removed. Instead of using the previous function, you can switch to using the {{<golink "Request">}} method with no further changes. For example, `bot.SetWebhook(config)` can be replaced with `bot.Request(config)`. 
* {{<golink "BotAPI.GetUpdatesChan">}} no longer returns an error. As it was always nil, there is no reason to include it. 
* There are no longer pointers to arrays, they were replaced with the array directly.
* {{<golink "MakeRequest">}} now takes in a {{<golink "Params">}} argument instead of `url.Values`. 
* {{<golink "UploadFile">}} now takes in a {{<golink "Params">}} argument instead of `map[string]string`. 

## New Features

* At the time of writing, every Telegram Bot API method and type is implemented.
* Any `Config` can be sent with {{<golink "Request">}}.

## Internal Library Improvements 

* Removed {{<golink "Chattable">}} `values()` and just use {{<golink "Chattable">}} `params()` instead. 
* Using new {{<golink "Params">}} type instead of a `map` or `url.Values` directly. This new type has a number of helper methods attached to it which greatly reduce the amount of boilerplate code for adding fields.


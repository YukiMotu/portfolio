---
title: "Blog Aggregator"
description: "Go, PostgreSQL, CLI App"
custom_link_label: ""
custom_link: ""
updatedDate: "Sep 11 2022"
pricing: "$10"
oldPricing: "$25.5"
checkoutUrl: "https://github.com/MizbaulHaqueMaruf/log_aggregator"
heroImage: "/Blog_Agg.jpg"
---

## Project Description
The Blog Aggregator is a CLI-based tool built in Go that collects and displays blog posts from various sources. It provides a simple and efficient way to keep up with multiple blogs in one place by fetching and aggregating RSS feeds.

## Commands

The Blog Aggregator provides several commands to interact with the system:

- **`login <username>`**: Log in with the specified username.  
- **`register <username>`**: Register a new user with the specified username.  
- **`users`**: List all registered users.  
- **`agg <time-between-reqs>`**: Start collecting feeds at the specified interval.  
- **`addfeed <name> <url>`**: Add a new feed with the specified name and URL.  
- **`feeds`**: List all available feeds.  
- **`follow <url>`**: Follow a feed with the specified URL.  
- **`following`**: List all feeds the current user is following.  
- **`unfollow <feed-url>`**: Unfollow the feed with the specified URL.  
- **`browse [limit]`**: Browse the latest posts with an optional limit.  


## How It Works  

The Blog Aggregator works by fetching RSS feeds from a list of predefined blog URLs. It parses the RSS feeds and displays the latest blog posts in a consolidated format.  

### Main Components:  

- **Fetcher**: Responsible for fetching the RSS feeds from the blog URLs.  
- **Parser**: Parses the fetched RSS feeds and extracts the relevant information.  
- **Aggregator**: Aggregates the parsed blog posts and prepares them for display.  
- **Server**: Serves the aggregated blog posts via a web interface.  




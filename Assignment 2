import tweepy
import json
import time
from datetime import datetime

# X API credentials
CONSUMER_KEY = "your_consumer_key"
CONSUMER_SECRET = "your_consumer_secret"
ACCESS_TOKEN = "your_access_token"
ACCESS_TOKEN_SECRET = "your_access_token_secret"

# Authenticate with X API
auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
auth.set_access_token(ACCESS_TOKEN, ACCESS_TOKEN_SECRET)
api = tweepy.API(auth, wait_on_rate_limit=True)  

# Define search parameters
SEARCH_QUERY = "climate change OR global warming OR #ClimateAction -filter:retweets"
MAX_TWEETS = 10000  # Target number of tweets to collect
TWEETS_PER_REQUEST = 100  # Max tweets per API call (X API limit)

# Function to extract relevant data from a tweet
def process_tweet(tweet):
    data = {
        "user_id": tweet.user.id_str,
        "username": tweet.user.screen_name,
        "tweet_id": tweet.id_str,
        "tweet_text": tweet.text,
        "timestamp": tweet.created_at.isoformat(),
        "retweet_user_id": None,  # Will be populated later for retweets
        "mentions": [user["id_str"] for user in tweet.entities["user_mentions"]]
    }
    return data

# Collect tweets
def collect_tweets(query, max_tweets, tweets_per_request):
    tweet_data = []
    total_collected = 0

    print(f"Starting tweet collection for query: '{query}'")
    
    # Use Cursor to paginate through search results
    for tweet in tweepy.Cursor(api.search_tweets, 
                              q=query, 
                              lang="en", 
                              tweet_mode="extended", 
                              count=tweets_per_request).items(max_tweets):
        try:
            # Handle retweets separately (extended tweet mode required)
            if hasattr(tweet, "retweeted_status"):
                original_tweet = tweet.retweeted_status
                data = process_tweet(original_tweet)
                data["retweet_user_id"] = tweet.user.id_str  # ID of user who retweeted
            else:
                data = process_tweet(tweet)
            
            tweet_data.append(data)
            total_collected += 1
            
            if total_collected % 100 == 0:
                print(f"Collected {total_collected} tweets...")

            # Stop if we hit the target
            if total_collected >= max_tweets:
                break

        except AttributeError as e:
            print(f"Error processing tweet: {e}")
            continue
        except tweepy.TweepyException as e:
            print(f"API error: {e}")
            time.sleep(60)  # Wait a minute before retrying
            continue

    return tweet_data

# Save data to JSON file
def save_to_json(data, filename):
    with open(filename, "w", encoding="utf-8") as f:
        json.dump(data, f, indent=4)
    print(f"Saved {len(data)} tweets to {filename}")

# Main execution
if __name__ == "__main__":
    # Record start time
    start_time = datetime.now()
    print(f"Collection started at: {start_time}")

    # Collect the data
    tweets = collect_tweets(SEARCH_QUERY, MAX_TWEETS, TWEETS_PER_REQUEST)

    # Save to file
    output_file = f"climate_tweets_{start_time.strftime('%Y%m%d_%H%M%S')}.json"
    save_to_json(tweets, output_file)

    # Report completion
    end_time = datetime.now()
    print(f"Collection completed at: {end_time}")
    print(f"Total time: {end_time - start_time}")
    print(f"Total tweets collected: {len(tweets)}")
​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​

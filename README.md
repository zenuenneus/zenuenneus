 ‎import requests
‎import openai
‎import time
‎from bs4 import BeautifulSoup
‎from googleapiclient.discovery import build
‎
‎# Blogger API Setup
‎BLOG_ID = "YOUR_BLOG_ID"
‎BLOGGER_API_KEY = "YOUR_BLOGGER_API_KEY"
‎OPENAI_API_KEY = "YOUR_OPENAI_API_KEY"
‎
‎# News Websites to Scrape
‎NEWS_SITES = [
‎    "https://www.aajtak.in/",
‎    "https://www.abplive.com/",
‎    "https://www.ndtv.com/",
‎    "https://www.jagran.com/",
‎    "https://www.foxnews.com/"
‎]
‎
‎# Function to Scrape News Articles
‎def scrape_news(url):
‎    response = requests.get(url)
‎    soup = BeautifulSoup(response.text, "html.parser")
‎    headlines = soup.find_all("h2")  # Modify based on actual structure
‎    news_list = []
‎    
‎    for headline in headlines:
‎        news_list.append(headline.get_text())
‎    
‎    return news_list[:5]  # Get top 5 headlines
‎
‎# Function to Rewrite Article using OpenAI
‎def rewrite_article(text):
‎    openai.api_key = OPENAI_API_KEY
‎    response = openai.ChatCompletion.create(
‎        model="gpt-4",
‎        messages=[
‎            {"role": "system", "content": "You are a professional news writer."},
‎            {"role": "user", "content": f"Rewrite this news article in a unique and engaging way: {text}"}
‎        ]
‎    )
‎    return response["choices"][0]["message"]["content"]
‎
‎# Function to Generate Image using DALL·E
‎def generate_image(prompt):
‎    response = openai.Image.create(
‎        prompt=prompt,
‎        n=1,
‎        size="1024x1024"
‎    )
‎    return response["data"][0]["url"]
‎
‎# Function to Post to Blogger
‎def post_to_blogger(title, content, image_url):
‎    blogger_service = build("blogger", "v3", developerKey=BLOGGER_API_KEY)
‎    post_body = {
‎        "kind": "blogger#post",
‎        "title": title,
‎        "content": f'<img src="{image_url}" /><br>{content}'
‎    }
‎    blogger_service.posts().insert(blogId=BLOG_ID, body=post_body).execute()
‎    print(f"Posted: {title}")
‎
‎# Main Function to Automate Everything
‎def main():
‎    for site in NEWS_SITES:
‎        news_articles = scrape_news(site)
‎        for article in news_articles:
‎            rewritten_article = rewrite_article(article)
‎            image_url = generate_image(article)
‎            post_to_blogger(article, rewritten_article, image_url)
‎            time.sleep(120)  # Wait for 2 minutes before next post
‎
‎if __name__ == "__main__":
‎    main()
‎

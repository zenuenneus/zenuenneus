- 👋 Hi, I’m @zenuenneus
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
zenuenneus/zenuenneus is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
name: Auto Blogger Post

on:
  schedule:
    - cron: '*/5 * * * *'  # हर 5 मिनट में रन होगा
  workflow_dispatch:

jobs:
  run_script:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v3
        with:
          python-version: '3.x'

      - name: Install Dependencies
        run: pip install requests beautifulsoup4 google-api-python-client openai

      - name: Run Script
        run: python main.py
        env:
          BLOG_ID: ${{ secrets.BLOG_ID }}
          BLOGGER_API_KEY: ${{ secrets.BLOGGER_API_KEY }}
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
